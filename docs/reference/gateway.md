# 网关配置

Hango 可对接多集群网关资源，默认创建一个 Gateway 资源（hango_envoy_gateway）；相同的 Hango 对接多个 Api-Plane 以对接多集群的 Envoy

通过界面的`网关管理`界面可以对 Hango 对接的网关进行管理，也可通过命令行的模式进行网关的创建，具体可参考[网关初始化脚本](https://github.com/hango-io/hango-gateway/blob/master/install/init-hango/init.sh)


![网关对接架构](../getting-started/imgs/config_网关对接.jpg)


网关资源包括如下配置

> 1.网关名称： 用于标识网关的名称
>
>2.网关地址： 用于访问集群中Hango envoy的URI
>
>3.ApiPlane地址： 对接Hango envoy的配置生成模块
>
>4.网关集群名称： 用于标识网关集群的名称
>
>5.域名列表： 访问网关需要携带的域名