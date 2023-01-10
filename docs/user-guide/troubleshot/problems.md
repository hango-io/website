# 问题案例

## 1.插件配置成功但未生效

插件在 Hango 前台（或接口）配置后，显示配置成功，但访问路由未生效

这种情况可能由多方面问题引起，可按照如下的流程进行排查

① 查看插件开关是否打开

登录k8s集群查看插件开关是否打开

```shell
## 查询到 pluginmanager 的名称
kubectl -n hango-system get pluginmanagers.microservice.slime.io

## 编辑或查看 pluginmanager 资源
kubectl -n hango-system edit pluginmanagers.microservice.slime.io ${pluginmanagerName}

## 确认指定插件的 enable 字段是否为 true，若未配置和 false 都代表插件未开启，需要修改，即时生效
```

② 检查 EnvoyPlugin 是否正常生成

```shell
kubectl get envoyplugins.microservice.slime.io -n hango-system
```

③ 查看 Envoy 是否存在拒绝配置的现象

```shell
## 查询到 Envoy 的 Pod 名
kubectl get po -n hango-system

## 查看日志中是否存在 rejected 关键词
kubectl -n hango-system logs -f ${envoyPodName}
```

## 2.服务发现没有注册中心

Hango 支持对接各类注册中心实现服务发现功能，在安装部署时需要确认开启或关闭状态，首先确认当前 Hango 的版本需要在 v1.1.0 版本或以上，详见如下文档操作

- Eureka: [对接eureka注册中心](../../user-guide/best-practices/service-route/eureka.md)
- Nacos: [对接nacos注册中心](../../user-guide/best-practices/service-route/nacos.md)
- Zookeeper(Dubbo): [对接zookeeper注册中心](../../user-guide/best-practices/service-route/dubbo-bridge.md)

## 3.注册中心无法查询到服务

注册中心服务 Hango 通过 Slime 对各类注册中心服务进行发现，可按照如下步骤进行检查

① 查看 Slime Pod 状态是否正常

```shell
kubectl get po -n hango-system
```

② 确认注册中心地址是否配置正确

通过如下命令查询 Slime 注册中心配置，其中 `Address` 是各注册中心的地址配置

```shell
kubectl get cm -n hango-system slime-hango -oyaml | grep -A1 "cfg_meshregistry: |"
```

③ 查看 Slime 是否有错误日志

```shell
## 查询到 Slime 的 Pod 名
kubectl get po -n hango-system

## 查看日志中的错误，根据错误进行问题修复或反馈
kubectl -n hango-system logs -f ${slimePodName}
```