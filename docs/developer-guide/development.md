# 本地开发

本文介绍如何在本地开发和调试Hango工程。

## 拉取代码

一般我们需要将 github 仓库进行 fork， 从主仓库 fork 出我们自己的 github 仓库，将其 clone 至本地代码仓库进行开发

## 确认运行环境

Hango 项目存在多种语言，包括 Java、C++、Golang 等；因此我们需要确保相关的语言环境完整

## 本地调试模块

Hango 的各模块对其他组件有一定的依赖关系，因此建议首先搭建一套完整的 Hango 环境，以单一组件的形式接入整套完整环境进行调试，将本地配置对接到环境配置进行调试；如下介绍如何搭建一套 Hango 环境

## 搭建 k8s 环境

若本地已经有搭建完成的 k8s 集群，并且可以参与调试，那是最好的 Hango 调试环境；若没有现成的 k8s 环境，我们也可以利用 [minikube](https://minikube.sigs.k8s.io/docs/start/) 来创建对应的调试环境

在 minikube 环境下我们仅需要如下命令即刻启动最小化的 k8s 集群，用于 Hango 运行

```
minikube start
```

## 运行 Hango

有了 k8s 环境后，我们可以很快搭建一套 Hango 环境，通过 [Hango-gateway](https://github.com/hango-io/hango-gateway/blob/master) 工程进行安装，通过 [install 脚本](https://github.com/hango-io/hango-gateway/blob/master/install/install.sh)实现快速安装

## 方法调试方式

修改评估后、修改内容和影响范围小的代码修改我们建议通过方法级别的测试来覆盖代码修改正确率，其中单元测试是我们推荐的方式，通过 Mock 的形式进行各链路的补充，专注于代码修改细节，让修改代码进行快速上线