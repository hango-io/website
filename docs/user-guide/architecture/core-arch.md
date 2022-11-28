# 设计架构

Hango 基于云原生理念构建，数据面基于 Envoy 进行扩展，增强插件链，提供 Rider 模块用于自定义插件扩展；控制面组件包括 Slime，Istio，API Plane 以及 Portal 模块。

得益于 [Slime](https://github.com/slime-io/slime) 良好的扩展性与兼容性，用户可以直接选择社区 [Istio](https://github.com/istio/istio) 作为控制面进行 Hango 构建。

![architecture](imgs/architecture.png)


## 组件说明

- Envoy: Hango网关的数据面流量入口，通过XDS模式获取Istiod配置
- Istiod: Hango网关对接Envoy的适配层，监听指定CRD并通过XDS向Envoy下发配置
- Slime: 提供指定Slime CRD，以支持Hango支持插件、限流、服务发现等特性
- Hango Portal: Hango对接前端界面的Web层模块，存储Hango业务数据
- Hango Api-plane: 对接Hango Portal，管理Hango CR声明周期