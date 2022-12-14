# 用户指南

该栏将为你展示关于Hango概念和使用相关的具体内容。:book:

## :projector: 架构与特性

Hango网关是怎么诞生的？了解一下[Hango的诞生背景](architecture/background.md)

想进一步了解Hango的技术架构和相关特性，请参考[设计架构](architecture/core-arch.md)和[优势与特性](architecture/advantages.md)

## :star2: 最佳实践

本章节将为你展示Hango的核心概念，包括[服务](best-practices/service-route/service.md)与[路由](best-practices/service-route/route.md)，各类服务发现能力及各类流量治理能力等

希望内部的Eureka服务暴露到网关？请参考[对接eureka注册中心](best-practices/service-route/eureka.md)

想在Hango使用网关的流量治理功能，可以参考[限流](best-practices/traffic-manage/rate-limit.md)、[熔断](best-practices/traffic-manage/circuit-breaker.md)、[降级](best-practices/traffic-manage/downgrade.md)和[流量染色](best-practices/traffic-manage/traffic-mark.md)等章节

对于安全防护有需求？可以通过waf插件支持，可参考[Dos](best-practices/security/Dos.md)栏

还有可观测性各方面的接入指导，可以通过[日志](best-practices/observability/loggie.md)和[指标](best-practices/observability/Prometheus.md)等文章进行学习

## :material-file-code: 扩展开发

Hango网关支持[自定义插件扩展](extension/plugin.md)，通过扩展 Rider 插件，可以快速将插件集成至 Hango 并生效使用。

## :man_construction_worker_tone3: 运维排障 

开发或使用过程中遇到了困难？可以先参考常见问题的[快速排障指南](../user-guide/troubleshot/log-collection.md)