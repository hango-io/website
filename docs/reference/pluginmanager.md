# 插件开关配置

Hango开放`PluginManger`资源开放插件开关，通过如下命令进行编辑

```shell
## 获取PluginManger实例名称
kubectl get pluginmanagers.microservice.slime.io -n hango-system

## 通过上一步骤名称对PluginManger实例进行编辑
kubectl edit pluginmanagers.microservice.slime.io -n hango-system {{ pluginmanager名称 }}
```

主要内容如下（部分内容），插件开关以yaml数组形式展示在PluginManager中，可通过配置对应插件的`enable`字段对插件进行开关，关闭后的插件及时配置路由插件也不会生效； 该配置修改即时生效

```yaml
  - enable: true
    inline:
      settings:
        ip_source_header: x-hango-real-ip
    listenerType: Gateway
    name: proxy.filters.http.iprestriction
    port: 80
  - enable: true
    listenerType: Gateway
    name: proxy.filters.http.locallimit
    port: 80
  - enable: true
    listenerType: Gateway
    name: proxy.filters.http.ua_restriction
    port: 80
```