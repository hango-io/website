# Dos

## Dos简介

DOS 攻击（Denial of Service 拒绝服务攻击）是一种网络攻击，它的目的是通过发送大量无效或伪造的数据请求，使得网络或计算机系统无法处理正常的数据流量，从而导致系统崩溃或无法正常运行。这种攻击通常会导致网站无法访问，甚至整个网络都会受到影响。

## WAF插件

Hango 通过集成 WAF 插件的方式支持防御 Dos 攻击；WAF（Web应用防火墙）插件是一种特殊的软件，它用于保护网站或Web应用程序免受恶意攻击的影响。它通过实时监测Web流量，并根据特定的规则和算法来识别和阻止恶意活动。WAF插件通常会包含一些基本的安全功能，例如防止SQL注入攻击和跨站点脚本攻击（XSS），并提供其他高级功能，如对抗DDoS攻击和安全日志记录等。

Dos 攻击防御是 WAF 插件中的一项功能，关于 Dos 的可配置项包括请求的`统计时间窗口`、`请求阈值`、`请求拒绝时间`这3项；我们做如下配置

| 配置项       | 配置值 |
|-----------|-----|
| 统计时间窗口(s) | 10  |
| 请求阈值      | 100 |
| 请求拒绝时间(s) | 5   |

基于上述配置，可以在 10s 内对访问 Hango 网关的请求数进行统计， 若请求数超过了 100 则将对其他请求进行拒绝，拒绝的时间窗为 5s 。通过拒绝请求的方式，可以在关键时刻对恶意的请求洪流进行拦截，起到保护系统的作用。

除了比较典型的 Dos 攻击防御功能外， WAF 插件还提供了非常多安全防护的能力。

## 具体配置


| 名称                                      | 类型      | 必选项 | 范围            | 描述                   |
|-----------------------------------------|---------|-----|---------------|----------------------|
| kind                                    | string  | 是   | waf           | 插件类型（值固定）            |
| wafRule.dosSwitch                       | boolean | 否   | -             | 是否打开dos功能            |
| wafRule.dosConfig.dos_burst_time_slice  | integer | 否   | [0, 3600]     | dos配置：统计时间窗          |
| wafRule.dosConfig.dos_counter_threshold | integer | 否   | [0, 99999999] | dos配置：请求数量阈值         |
| wafRule.dosConfig.dos_block_timeout     | integer | 否   | [0, 86400]    | dos配置：请求拒绝时间         |
| scannerSwitch                           | boolean | 否   | -             | 防止扫描攻击开关             |
| lfiSwitch                               | boolean | 否   | -             | LFI攻击开关              |
| rfiSwitch                               | boolean | 否   | -             | RFI攻击开关              |
| rceSwitch                               | boolean | 否   | -             | RCE攻击开关              |
| phpInjectionSwitch                      | boolean | 否   | -             | PHP代码注入攻击开关          |
| xssSwitch                               | boolean | 否   | -             | XSS攻击开关              |
| sqliSwitch                              | boolean | 否   | -             | SQL注入攻击开关            |
| sessionFixationSwitch                   | boolean | 否   | -             | Session Fixation攻击开关 |
| javaInjectionSwitch                     | boolean | 否   | -             | Java代码注入攻击开关         |
| cgiDataLeakagesSwitch                   | boolean | 否   | -             | CGI数据泄露攻击开关          |
| sqlDataLeakagesSwitch                   | boolean | 否   | -             | SQL数据泄露攻击开关          |
| javaDataLeakagesSwitch                  | boolean | 否   | -             | JAVA数据泄露攻击开关         |
| phpDataLeakagesSwitch                   | boolean | 否   | -             | PHP数据泄露攻击开关          |
| iisDataLeakagesSwitch                   | boolean | 否   | -             | IIS数据泄露攻击开关          |

对于各项开关功能有对应如下释义

| 功能               | 解释                                                                                              | CRS id  | 配置  |
|------------------|-------------------------------------------------------------------------------------------------|---------|-----|
| DOS              | 防止拒绝服务攻击，针对 ip 级别的请求数统计实现防御                                                                     | 912     | 是   |
| Scanner          | 防止扫描攻击，检查某些开源或商业的扫描攻击的默认 User-Agent 和 Headers                                                   | 913     | 否   |
| LFI              | 防止利用本地文件包含漏洞进行攻击，检查headers、cookie或者 uri 中是否包含目录遍历、系统文件等                                         | 930     | 否   |
| RFI              | 防止利用远程文件包含漏洞进行攻击，检查 url 参数中是否包含 ip 地址、body 中是否包含 include关键字等                                    | 931     | 否   |
| RCE              | 防止利用远程命令执行漏洞进行攻击，检查 cookie、url 参数等是否包含Unix Command、Windows Command、Windows PowerShell Command 等 | 932     | 否   |
| PHP Injection    | 防止 PHP 代码注入 ，检查 headers、cookie、rl 参数等是否包含可能的 php 文件、关键字之类                                       | 933     | 否   |
| XSS              | 防止跨站脚本攻击，检查 headers、cookie、rl 参数等是否包含可能的网页脚本                                                    | 941     | 否   |
| SQLi             | 防止 SQL 注入，检查 headers、cookie、rl 参数等是否包含可能的 sql 语句等                                               | 942     | 否   |
| Session Fixation | 阻止利用Session会话ID不变的漏洞进行攻击                                                                        | 943     | 否   |
| Java Injection   | 防止 Java 代码注入，检查 headers、cookie、rl 参数等是否包含可能的 java 文件、关键字之类                                      | 944     | 否   |
| Data Leakages    | 防止数据泄露（CGI、SQL、JAVA、PHP、IIS），检查 response body 中是否包含相应语言的关键字或者错误输出等                              | 950-954 | 否   |

## 前置条件

以下curl命令中存在变量，使用前需要替换为真实环境数据，变量以`{{}}`符号包裹，例如`{{ 网关ID }}`需要根据网关的实际ID进行替换；创建插件命令中`PluginConfiguration`（插件配置）需要根据实际需要进行配置

## 创建

```shell
curl -XPOST -v -H "Content-Type:application/json" -d '{
  "BindingObjectId": {{ 路由ID }},
  "BindingObjectType": "routeRule",
  "GwId": {{ 网关ID }},
  "PluginConfiguration": "{\"wafRule\":{\"dosSwitch\":true,\"dosConfig\":{\"dos_burst_time_slice\":\"10\",\"dos_counter_threshold\":\"100\",\"dos_block_timeout\":\"5\"},\"scannerSwitch\":true,\"lfiSwitch\":true,\"rfiSwitch\":true,\"rceSwitch\":true,\"phpInjectionSwitch\":true,\"xssSwitch\":true,\"sqliSwitch\":true,\"sessionFixationSwitch\":true,\"javaInjectionSwitch\":true,\"cgiDataLeakagesSwitch\":true,\"sqlDataLeakagesSwitch\":true,\"javaDataLeakagesSwitch\":true,\"phpDataLeakagesSwitch\":true,\"iisDataLeakagesSwitch\":true},\"kind\":\"waf\"}",
  "PluginType": "waf"
}' http://{{ hango-portal ip:port }}/gdashboard?Action=BindingPlugin&Version=2019-09-01
```

## 测试

```shell
## 如下命令展示了用命令调用 100 次请求的案例，可修改 for 中的请求次数，进行 Dos 测试，测试是否会进行请求拒绝
for((i=0;i<100;i++));do curl -v "http://{{ 网关IP }}/{{ 路由path }}" -H "host:{{ 网关关联域名 }}";done
```

## 删除

```shell
curl -v -H "Content-Type:application/json" http://{{ hango-portal ip:port }}/gdashboard?PluginBindingInfoId={{ 插件ID }}&Action=UnbindingPlugin&Version=2019-09-01
```

## 界面配置方法

详见hango-gateway工程文档[插件配置方法](https://github.com/hango-io/hango-gateway/blob/master/docs/plugin/plugin-configuring-guide.md)