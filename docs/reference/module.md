# 功能模块开关

Hango 通过 bundle 模式 Slime 开启了插件、限流功能及注册中心服务发现功能，通过如下命令进行编辑

```shell
kubectl edit cm -n hango-system slime-hango
```

主要配置内容如下

```yaml
data:
  cfg: |
    {"bundle":{"modules":[{"kind":"limiter","name":"limiter"},{"kind":"plugin","name":"plugin"}]},"enable":true,"global":{"configSources":[{"address":"ss://"}],"log":{"logLevel":"info"}},"name":"bundle"}
  cfg_limiter: |
    {"enable":true,"general":{"disableAdaptive":true,"disableGlobalRateLimit":true,"disableInsertGlobalRateLimit":true},"kind":"limiter","mode":"BundleItem","name":"limiter"}
  cfg_plugin: |
    {"enable":true,"kind":"plugin","mode":"BundleItem","name":"plugin"}
kind: ConfigMap
```

`cfg_limiter`和`cfg_plugin`为限流和插件的配置内容，通过修改`cfg`中的`modules`对模块功能进行开关，修改配置并重启Slime组件后开关生效

```shell
## 重启Slime模块命令（k8s删除后会新建Pod）
kubectl delete po -n hango-system {{ Slime Pod名称 }}
```