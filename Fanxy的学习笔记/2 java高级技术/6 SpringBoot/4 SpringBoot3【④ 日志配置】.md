# 4 SpringBoot3【④ 日志配置】
> 规范：项目开发不要编写`System.out.println()`，应该用 **`日志`** 记录信息

| 日志门面                                                     | 日志实现 (没有对应关系)                  |
| ------------------------------------------------------------ | ---------------------------------------- |
| JCL(Jakarta Commons Logging)                                 | Log4j                                    |
| <font color="#dd0000">**SLF4j(Simple Logging Facade for Java)**</font> | JUL(java.util.logging)                   |
| jboss-logging                                                | Log4j2                                   |
|                                                              | <font color="#dd0000">**Logback**</font> |

感兴趣日志框架关系与起源可参考：[SpringBoot2雷神视频 视频 21~27集](https://www.bilibili.com/video/BV1gW411W76m) 


## 1.  简介
1. Spring使用`commons-logging`作为内部日志，但底层日志实现是开放的。可对接其他日志框架。

    `spring5` 及以后 `commons-logging`被`spring`直接自己写了。

2. 支持`jul`，`log4j2`,`logback`。`SpringBoot` 提供了默认的控制台输出配置，也可以配置输出为文件。

3. `logback`是默认使用的。

4. 虽然**日志框架很多**，但是我们不用担心，使用 `SpringBoot` 的 **默认配置就能工作的很好**。

**SpringBoot怎么把日志默认配置好的**
1、每个`starter`场景，都会导入一个核心场景`spring-boot-starter`

2、核心场景引入了日志的所用功能`spring-boot-starter-logging`

3、默认使用了`logback + slf4j `组合作为默认底层日志

4、**`日志是系统一启动就要用`**，`xxxAutoConfiguration`是 **系统启动好了以后放好的组件，后来用的。**

5、日志是利用 **监听器机制** 配置好的。`ApplicationListener`。

6、日志所有的配置都可以通过修改配置文件实现。**以`logging`开始的所有配置。**

## 2. 日志格式
```shell
2023-03-31T13:56:17.511+08:00  INFO 4944 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-03-31T13:56:17.511+08:00  INFO 4944 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.7]
```
默认输出格式：
- 时间和日期：毫秒级精度
- 日志级别：`ERROR, WARN, INFO, DEBUG,` or `TRACE`.
- 进程 ID `(其实在cmd里面，我们使用jps命令可以看到当前所有的java进程)`
- ---： 消息分割符
- 线程名： 使用 **[   ]** 包含
- Logger 名： 通常是产生日志的 **类名**
- 消息： 日志记录的内容
注意： logback 没有`FATAL`级别，对应的是`ERROR`

默认值：参照：`spring-boot`包`additional-spring-configuration-metadata.json`文件
默认输出格式值：`%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd'T'HH:mm:ss.SSSXXX}}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}`
可在配置文件通过logging修改为：`'%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{15} ===> %msg%n'`

`-5` 代表左对齐五个字符

`%logger`  代表当前类

`%n` 换行

## 3. 记录日志
```java
Logger logger = LoggerFactory.getLogger(getClass());
```
**或者使用Lombok的@Slf4j注解**

## 4. 日志级别
- 由低到高：`ALL,TRACE, DEBUG, INFO, WARN, ERROR,FATAL,OFF`；
  - 只会打印指定级别及以上级别的日志
  - <font color="#0000dd">ALL：打印所有日志</font>
  - <font color="#006600">TRACE：追踪框架详细流程日志，一般不使用</font>
  - <font color="#006600">DEBUG：开发调试细节日志</font>
  - <font color="#006600">INFO：关键、感兴趣信息日志</font>
  - <font color="#006600">WARN：警告但不是错误的信息日志，比如：版本过时</font>
  - <font color="#006600">ERROR：</font><font color="#dd0000">业务错误日志，比如出现各种异常</font>
  - <font color="#dd0000">FATAL：致命错误日志，比如jvm系统崩溃</font>
  - <font color="#dd0000">OFF：关闭所有日志记录</font>
- 不指定级别的所有类，都使用root指定的级别作为默认级别
- `SpringBoot`日志 **默认级别是 `INFO`**

1. 在`application.properties/yaml`中配置`logging.level.<logger-name>=<level>`指定日志级别，其中`<logger-name>`可以指定包名，或者是下面的root
2. `level`可取值范围：`TRACE, DEBUG, INFO, WARN, ERROR, FATAL, or OFF`，定义在 `LogLevel `类中
3. root 的`logger-name`叫`root`，可以配置`logging.level.root=warn`，代表所有未指定日志级别都使用 root 的 `warn` 级别

## 5. 日志分组
比较有用的技巧是：
将相关的`logger`分组在一起，统一配置。SpringBoot 也支持。比如：Tomcat 相关的日志统一设置

```java
logging.group.tomcat=org.apache.catalina,org.apache.coyote,org.apache.tomcat
logging.level.tomcat=trace
```
SpringBoot 预定义两个组
| Name | Loggers                                                      |
| ---- | ------------------------------------------------------------ |
| web  | `org.springframework.core.codec, org.springframework.http, org.springframework.web, org.springframework.boot.actuate.endpoint.web, org.springframework.boot.web.servlet.ServletContextInitializerBeans` |
| sql  | `org.springframework.jdbc.core, org.hibernate.SQL, org.jooq.tools.LoggerListener` |
## 6. 文件输出
SpringBoot 默认只把日志写在控制台，如果想额外记录到文件，可以在`application.properties`中添加`logging.file.name` or `logging.file.path`配置项。

| ` logging.file.name` | `logging.file.path` | 示例       | 效果                                              |
| -------------------- | ------------------- | ---------- | ------------------------------------------------- |
| 未指定               | 未指定              |            | 仅控制台输出                                      |
| **指定**             | 未指定              | `my.log `  | 写入指定文件。可以加路径。不加路径为当前项目路径  |
| 未指定               | **指定**            | `/var/log` | 写入指定目录，文件名为spring.log                  |
| **指定**             | **指定**            |            | 以`logging.file.name为准，和path的值没有任何关系` |

## 7. 文件归档与滚动切割
> 归档：每天的日志单独存到一个文档中。
> 切割：每个文件10MB，超过大小切割成另外一个文件。
1. 每天的日志应该独立分割出来存档。如果使用`logback`（SpringBoot 默认整合），可以通过`application.properties/yaml`文件指定日志滚动规则。
2. 如果是其他日志系统，需要自行配置（添加`log4j2.xml`或`log4j2-spring.xml`）
3. 支持的滚动规则设置如下

| 配置项                                                  | 描述                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| `logging.logback.rollingpolicy.file-name-pattern`       | 日志存档的文件名格式（默认值：`${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz`） |
| ` logging.logback.rollingpolicy.clean-history-on-start` | 应用启动时是否清除以前存档（默认值：`false`）                |
| `logging.logback.rollingpolicy.max-file-size`           | 存档前，每个日志文件的最大大小（默认值：`10MB`）             |
| `logging.logback.rollingpolicy.total-size-cap`          | 日志文件被删除之前，可以容纳的最大大小（默认值：`0B`）。设置1GB则磁盘存储超过 `1GB` 日志后就会删除旧日志文件 |
| `logging.logback.rollingpolicy.max-history`             | 日志文件保存的最大天数(默认值：`7`).                         |
## 8. 自定义配置
通常我们配置 `application.properties `就够了。当然也可以自定义。比如：
| 日志系统                | 自定义                                                       |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml, logback-spring.groovy, logback.xml, `or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

如果可能，我们建议您在日志配置中使用`-spring` 变量（例如，`logback-spring.xml` 而不是 `logback.xml`）。如果您使用标准配置文件，spring 无法完全控制日志初始化。
最佳实战：自己要写配置，配置文件名加上` xx-spring.xml`

## 9. 切换日志组合
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

log4j2支持yaml和json格式的配置文件

| 格式 | 依赖                                                         | 文件名                   |
| ---- | ------------------------------------------------------------ | ------------------------ |
| YAML | com.fasterxml.jackson.core:jackson-databind + com.fasterxml.jackson.dataformat:jackson-dataformat-yaml | log4j2.yaml + log4j2.yml |
| JSON | com.fasterxml.jackson.core:jackson-databind                  | log4j2.json + log4j2.jsn |

## 10. 最佳实战
1. 导入任何第三方框架，先排除它的日志包，因为Boot底层控制好了日志
2. 修改 `application.yaml `配置文件，就可以调整日志的所有行为。如果不够，可以编写日志框架自己的配置文件放在类路径下就行，比如`logback-spring.xml`，`log4j2-spring.xml`
3. 如需对接 **专业日志系统** ，也只需要把 logback 记录的 **日志** 灌倒 **kafka** 之类的中间件，这和SpringBoot没关系，都是日志框架自己的配置， **修改配置文件即可**
4. **业务中使用slf4j-api记录日志。不要再 sout 了**