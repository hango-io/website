# 注册中心对接

Hango 的可从不同的注册中心发现服务，将其映射为网关的服务概念，其中包括 Http 类型服务和 Dubbo 类型服务，多协议正在逐步扩展中。

> 若不清楚如何安装Hango，请先参考[Hango安装流程指导](https://github.com/hango-io/hango-gateway/blob/master/install/README.zh_CN.md)

## 对接k8s

Hango 默认支持 k8s 服务获取，无需配置开关

## 对接eureka

当前 Hango 安装部署阶段决定是否开启 eureka 对接模式，网关请参考需修改[hango-gateway工程](https://github.com/hango-io/hango-gateway)中的安装部署配置来决定是否开启对接

```shell
## 配置文件路径
install/helm/hango-gateway/charts/hango-gateway/values.yaml
```

eureka主要的配置内容如下，`enable`字段代表是否开启对接，若声明开启对接eureka则需要填写正确的`address`，否则会导Hango Slime致组件启动异常

```yaml
registry:
  eureka:
    ## eureka注册中心功能开关（true 或 false）
    enable: false
    ## 刷新eureka服务缓存的时间周期，默认15s，格式为: [正整数]s
    refresh_period: 15s
    ## 注册中心实例地址；格式：整体以英文单引号包裹，单个地址以英文双引号包裹，多个地址用英文逗号分隔
    address: '"http://127.0.0.1:8761/eureka"'
```

## 对接nacos

当前Hango安装部署阶段决定是否开启nacos对接模式，网关请参考需修改[hango-gateway工程](https://github.com/hango-io/hango-gateway)中的安装部署配置来决定是否开启对接

```shell
## 配置文件路径
install/helm/hango-gateway/charts/hango-gateway/values.yaml
```

nacos主要的配置内容如下，`enable`字段代表是否开启对接，若声明开启对接nacos则需要填写正确的`address`，否则会导Hango Slime致组件启动异常

注意：当前版本必须指定Nacos注册中的`Group`属性，不然仅从默认组获取服务

```yaml
registry:
  nacos:
    ## nacos注册中心功能开关（true 或 false）
    enable: false
    ## 注册中心实例地址；格式：整体以英文单引号包裹，单个地址以英文双引号包裹，多个地址用英文逗号分隔
    address: '"http://127.0.0.1:8848"'
    ## nacos客户端服务所在分组
    service_group: hango_demo
```

## 对接Zookeeper(Dubbo)

当前Hango安装部署阶段决定是否开启zookeeper对接模式，网关请参考需修改[hango-gateway工程](https://github.com/hango-io/hango-gateway)中的安装部署配置来决定是否开启对接

```shell
## 配置文件路径
install/helm/hango-gateway/charts/hango-gateway/values.yaml
```

zookeeper主要的配置内容如下，`enable`字段代表是否开启对接

```yaml
registry:
  zk:
    ## zookeeper注册中心功能开关（true 或 false）
    enable: false
    ## 刷新zookeeper服务缓存的时间周期，默认15s，格式为: [正整数]s
    refresh_period: 15s
    ## 注册中心实例地址；格式：整体以英文单引号包裹，单个地址以英文双引号包裹，多个地址用英文逗号分隔
    address: "zk:2181"
```