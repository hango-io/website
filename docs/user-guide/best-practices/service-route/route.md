# 路由管理

## 1.路由概念

`路由`是Hango网关的核心概念之一，代表着通过Hango网关的请求转发和选择路径的方式，是服务的下级概念，必须与`已发布服务`绑定

Hango网关的路由存在2种形式，如下列表所示

- 路由元数据（路由管理界面）
- 已发布路由（已发布路由管理）

`路由元数据`是携带唯一标识的数据，其信息包括路由的匹配条件（path\method\header等），但并未配置至Hango数据面（envoy）；

`已发布路由`基于`路由元数据`生成，代表着真正生效的路由内容，其信息包括超时时间、重试条件等

下线已发布路由不会对路由元数据产生影响，修改路由元数据不会对已发布路由产生影响，这2种形态的路由是解耦的概念

## 2.创建路由

通过`路由管理`界面可以创建路由元数据，必须声明路由元数据所属的已发布服务，以及路由的匹配条件，通过如下图操作创建路由元数据

![创建路由](../imgs/创建路由.png)

## 3.发布路由

基于已经创建好的路由元数据，可以将其发布至指定网关，从而创建对应的已发布路由，如下图所示

![发布路由](../imgs/发布路由.png)

发布路由支持配置路由的超时时间、重试机制

## 4.下线路由

`已发布路由管理`界面可以对已发布的路由进行下线，点击如下操作按钮即可对路由进行下线

![下线路由](../imgs/下线路由.png)

## 5.访问路由

> 注意：网关地址已暴露到集群外，否则仅在集群内可访问

路由发布后，已经可以通过Hango网关对指定已发布路由进行访问

通过`网关管理`界面获取到当前网关的访问信息，包括地址和域名，以如下方式进行访问（以curl命令为例）

```shell
## {{}} 需要根据实际环境替换变量
curl "{{ 网关地址 }}/{{ 路由path }}" -H "{{ 网关域名 }}"
```