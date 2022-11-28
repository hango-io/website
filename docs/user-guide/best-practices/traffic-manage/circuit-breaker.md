# 熔断

随着分布式服务架构的变化和演进，服务数量和链路变得复杂化，服务间的可用性也需要更多的保障，熔断机制成为保证上游服务正常可用的一种常规措施

## 熔断配置

Hango以`熔断插件(circuit-breaker)`的形式提供了请求熔断方案，包含了对于下游服务错误率、响应时间等维度熔断场景的保障，具体配置如下


| 名称                               | 类型       | 必选项 | 范围                                              | 描述                                                                            |
|----------------------------------|----------|-----|-------------------------------------------------|-------------------------------------------------------------------------------|
| kind                             | string   | 是   | circuit-breaker                                 | 插件类型（值固定）                                                                     |
| response.headers[].key           | string   | 否   | length <= 200                                   | 熔断后请求响应头名称                                                                    |
| response.headers[].value         | string   | 否   | length <= 200                                   | 熔断后请求响应头值                                                                     |
| response.code                    | integer  | 是   | [200, 599]                                      | 熔断后请求响应码                                                                      |
| response.body                    | string   | 否   | -                                               | 熔断后请求响应body                                                                   |
| config.average_response_time     | float    | 否   | (0, 1000.0] (s)                                 | 慢响应时间阈值                                                                       |
| config.error_percent_threshold   | float    | 否   | (0, 100.0]                                      | 错误百分比阈值                                                                       |
| config.consecutive_slow_requests | integer  | 否   | [1, 10000]                                      | 连续慢响应次数                                                                       |
| config.breakType                 | []string | 是   | ErrorPercentCircuitbreaker<br/>RTCircuitbreaker | 熔断触发条件类型<br/>ErrorPercentCircuitbreaker: 错误率触发熔断<br/>RTCircuitbreaker: RT触发熔断 |
| config.min_request_amount        | integer  | 否   | [1, 1000000]                                    | 最小请求次数                                                                        |
| config.lookback_duration         | integer  | 是   | [1, 120] (s)                                    | 最小请求次数                                                                        |
| config.break_duration            | integer  | 是   | [1, 7200] (s)                                   | 惩罚时间                                                                          |


## 前置条件

以下curl命令中存在变量，使用前需要替换为真实环境数据，变量以`{{}}`符号包裹，例如`{{ 网关ID }}`需要根据网关的实际ID进行替换；创建插件命令中`PluginConfiguration`（插件配置）需要根据实际需要进行配置

## 创建

```shell
curl -XPOST -v -H "Content-Type:application/json" -d '{
  "BindingObjectId": {{ 路由ID }},
  "BindingObjectType": "routeRule",
  "GwId": {{ 网关ID }},
  "PluginConfiguration": "{\"kind\":\"circuit-breaker\",\"response\":{\"headers\":[{\"key\":\"breakHeader\",\"value\":\"breakHeaderValue\"}],\"code\":\"510\",\"body\":\"breakBody\"},\"config\":{\"average_response_time\":\"60\",\"error_percent_threshold\":\"90.0\",\"consecutive_slow_requests\":\"5\",\"breakType\":[\"ErrorPercentCircuitbreaker\",\"RTCircuitbreaker\"],\"min_request_amount\":\"20\",\"lookback_duration\":\"60\",\"break_duration\":\"10\"}}",
  "PluginType": "circuit-breaker"
}' http://{{ hango-portal ip:port }}/gdashboard?Action=BindingPlugin&Version=2019-09-01
```

## 测试

```shell
## 通过改造如下基础curl命令，创造插件中配置数量的错误次数，根据时间窗或错误请求次数验证插件是否造成熔断
curl -v "http://{{ 网关IP }}/{{ 路由path }}" -H "host:{{ 网关关联域名 }}"
```

## 删除

```shell
curl -v -H "Content-Type:application/json" http://{{ hango-portal ip:port }}/gdashboard?PluginBindingInfoId={{ 插件ID }}&Action=UnbindingPlugin&Version=2019-09-01
```

## 界面配置方法

详见hango-gateway工程文档[插件配置方法](https://github.com/hango-io/hango-gateway/blob/master/docs/plugin/plugin-configuring-guide.md)