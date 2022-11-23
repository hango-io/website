# 对接kubernetes注册中心

Hango网关支撑对接kubernetes（后续统称k8s），通过对接k8s获取service类型服务

## 1.配置开关

Hango默认支持k8s服务获取，无需配置开关

## 2.使用指导

Hango界面中发布服务，可以看到`Kubernetes`选项，选择后可查询并发布k8s类型服务到网关，如下图所示

![k8s](../imgs/k8s.png)

发布服务成功后，创建路由并绑定至该k8s类型已发布服务，则可以通过Hango网关对其进行访问