# 黑白名单

Hango 提供了多款黑白名单插件用于对请求进行限制，包括 URI 、 UA 、 Header 、 Referer 等等。

## 操作案例

以请求头黑白名单为例，其他黑白名单插件请参考[插件指引](https://github.com/hango-io/hango-gateway/tree/master/docs)。我们希望对请求头中携带"block:block"信息的请求进行拦截，我们可以对路由配置Header黑白名单插件（`header-restriction`）实现。

## 属性

首先从插件属性开始介绍，其中 `type` 代表黑白名单的类型，而 `list` 则是数组配置，代表多组 `header` 的配置。

| 名称                | 类型      | 必选项 | 范围                                                | 描述                           |
|-------------------|---------|-----|---------------------------------------------------|------------------------------|
| kind              | string  | 是   | header-restriction                                | 插件类型（值固定）                    |
| type              | integer | 是   | [0, 1]                                            | Header黑白名单策略<br/>0:黑名单；1:白名单 |
| list[].header     | string  | 是   | length <= 200                                     | Header名                      |
| list[].match_type | string  | 是   | exact_match<br/>prefix_match<br/>safe_regex_match | Header黑白名单策略                 |
| list[].value      | string  | 是   | length <= 200                                     | Header值                      |

## 前置条件

以下curl命令中存在变量，使用前需要替换为真实环境数据，变量以`{{}}`符号包裹，例如`{{ 网关ID }}`需要根据网关的实际ID进行替换；创建插件命令中`PluginConfiguration`（插件配置）需要根据实际需要进行配置

## 创建

```shell
curl -XPOST -v -H "Content-Type:application/json" -d '{
  "BindingObjectId": {{ 路由ID }},
  "BindingObjectType": "routeRule",
  "GwId": {{ 网关ID }},
  "PluginConfiguration": "{\"type\":\"0\",\"list\":[{\"header\":\"block\",\"match_type\":\"exact_match\",\"value\":[\"block\"]}],\"kind\":\"header-restriction\"}",
  "PluginType": "header-restriction"
}' http://{{ hango-portal ip:port }}/gdashboard?Action=BindingPlugin&Version=2019-09-01
```

## 测试

```shell
## 修改如下请求的Header参数，与插件配置的一致，测试Header黑白名单效果
curl -v "http://{{ 网关IP }}/{{ 路由path }}" -H "host:{{ 网关关联域名 }}" -H "block:block"
```

## 删除

```shell
curl -v -H "Content-Type:application/json" http://{{ hango-portal ip:port }}/gdashboard?PluginBindingInfoId={{ 插件ID }}&Action=UnbindingPlugin&Version=2019-09-01
```

## 界面配置方法

[插件界面配置方法引导](plugin-configuring-guide.md)