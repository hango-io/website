# 数据库对接

Hango 默认安装对接内存数据库 H2，可通过更改配置对接其他类型数据库，以下以 MySQL 为例

> 依赖：除MySQL数据库外，其他数据库需要在 Hango-portal 工程中引入驱动依赖，重新打包

## 1.导入数据表依赖

在MySQL 客户端中执行[数据表](https://github.com/hango-io/portal/blob/main/gateway-portal/src/main/resources/schema.sql)和[初始数据](https://github.com/hango-io/portal/blob/main/gateway-portal/src/main/resources/data.sql)

## 2.通过如下命令修改hango-portal的ConfigMap

```yaml
kubectl edit cm -n hango-system hango-portal-config
```

## 3.修改如下配置
```yaml
## H2配置
spring.datasource.platform=h2
spring.datasource.url=jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=false;MODE=MYSQL;TRACE_LEVEL_FIle=4;TRACE_LEVEL_SYSTEM_OUT=3
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=root

## MySQL配置
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://[your mysql uri]/apigw_gportal?&autoReconnect=true&connectTimeout=5000&socketTimeout=50000&generateSimpleParameterMetadata=true
spring.datasource.username=[your username]
spring.datasource.password=[your password]
```

## 4.重启hango-portal

通过如下命令重启 hango-portal
```yaml
kubectl delete po -n hango-system [hango-portal Pod名称]
```

## 5.通过脚本创建初始网关

可通过[网关初始化脚本](https://github.com/hango-io/hango-gateway/blob/master/install/init-hango/init.sh)创建初始网关对象；也可通过手动配置[创建网关](#3.网关配置)