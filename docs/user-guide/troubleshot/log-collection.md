# 快速排障指南

### 1. 控制台操作报错

+ 1、 找到请求对应的traceId。

  当控制台操作报错且是可复现问题，则可以F12（Chrome浏览器）打开开发者调试窗口并尝试重试，选择【Network】 -> 【XHR】 ，并点击失败的请求（一般红色），在【Preview】中通常情况下会有`RequestId`字段，该字段为请求的traceid。

+ 2、 使用traceId查询Hango-Portal日志，然后根据具体日志进行错误原因分析

  登录到部署Portal的k8s集群中，找到Portal的pod，并查看日志。
  > 注意：当Portal是多副本部署时，需要进入到多个pod中进行查询

```
## 查询gportal的pod，第一列为podName
kubectl -n hango-system get pod | grep portal

## 进入到gportal的pod
kubectl -n hango-system exec -it ${podName} bash

## 进入到logs目录
cd logs
```

### 2. 插件不生效

当对某个路由配置插件后不生效，通常有如下几种原因：

#### 2.1、 控制台页面中插件未绑定成功

重新到插件绑定页面中确认插件是否绑定成功

#### 2.2、 插件开关未打开

+ 1、 登录k8s集群查看插件开关是否打开

```
## 查询到pluginmanager的名称
kubectl -n hango-system get pluginmanagers.microservice.slime.io

## 编辑或查看pluginmanager资源
kubectl -n hango-system edit pluginmanagers.microservice.slime.io ${pluginmanagerName}

## 确认指定插件的enable字段是否为true，若未配置和false都代表插件未开启，需要修改，即时生效
```


#### 2.3、 插件绑定成功，但是api-plane未正确创建对应crd

  登录到部署api-plane的k8s集群中，找到api-plane的pod，并查看日志。
  > 注意：当api-plane是多副本部署时，需要找到对应流量的pod进行查看

```
## 查询api-plane的pod，第一列为podName
kubectl -n hango-system get pod | grep api-plane

## 直接查看控制台输出日志
kubectl -n hango-system logs -f ${podName}
```

#### 2.4、 插件配置未下发到envoy

+ 1、 插件pod列表，找到目标pod name，若多副本部署则任意挑选一个副本即可：
```    
kubectl -n hango-system get pod
```

+ 2、 查看日志确认是否由于配置错误导致Envoy拒绝

```
## 若日志中出现rejected关键字，则通常是由于配置错误导致Envoy拒绝接收，会影响该次配置及后续的配置操作，请及时定位问题并处理
kubectl -n hango-system logs -f ${podName} | grep rejected
```

+ 3、 dump出pod中Envoy的配置
```
kubectl exec -it ${podName} -n ${namespace} -- curl localhost:20000/config_dump > config_dump.json
```

+ 4、 查看dump处理的配置文件，搜索`http_filters`，看该字段是否有name为对应插件的属性，若没有则该插件未打开

### 3. 路由不生效
路由是否生效需要结合配置dr与vs资源以及istiod下发至envoy进行查询。
#### 3.1、 路由返回404
如果返回404 NR，代表不存在该路由。<br>
1）首先进入集群，kubectl get vs -n hango-system | grep apiid 查询具体vs配置，查看vs http route是否和预期相同，如果相同则转2）<br>
2）查询配置是否下发至envoy。查找envoy 容器pod，执行kubectl exec -it podname -n hango-system -- curl localhost:20000/config_dump | less指令，查询是否有对应的路由配置，若不存在 则转3）<br>
3）查询istiod，是否有配置拒绝的情况。查找istiod容器，kubectl logs -f podname -n hango-system 查询是否存在error<br>

#### 3.2、 路由返回503
如果返回503，需要查询response flag。如果response flag为NR，代表no cluster，即服务cluster配置不正确。
需要进入envoy容器排查：<br>

（1）accessLog匹配的路由id；进入envoy容器，查看/tmp/accessLog<br>
（2）curl localhost:20000/config_dump | less, 搜索路由id，查询对应的cluster<br>
（3）curl localhost:20000/clusters | less， 搜多对应cluster是否存在<br><br>
UH代表无可用upstream，即后端节点都不存在。

#### 3.3、 请求返回的response flag相关对应关系
具体的response flag对应关系如下：

| 错误码  | 含义                     |
|------|------------------------|
| UH   | 无可用健康的上游服务实例           |
| UF   | 连接失败                   |
| UO   | 超出最大可pending请求         |
| NR   | 无路由（404）或者服务未正确发布（503） |
| URX  | 超过最大重试次数               |
| DC   | 客户端断开连接                |
| LH   | 本地服务健康检查失败             |
| UT   | 请求超时                   |
| LR   | 本地重置连接                 |
| UR   | 服务端重置连接                |
| UC   | 上游连接中止                 |
| DI   | 请求被注入延迟后处理             |
| FI   | 错误注入                   |
| RL   | 限流                     |
| UAEX | 被鉴权插件禁止访问              |
| RLSE | 限流服务内部出错               |
| IH   | 对于严格检查的header设置了不可用的值  |
| SI   | 请求流空闲时间超时              |
| DPE  | 下游请求存在HTTP协议错误         |
