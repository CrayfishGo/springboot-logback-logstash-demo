# springboot-logback-logstash-demo
How does the logback transfer the log to logstash? Try this demo

在运行本示例之前，请参考[docker-elk](https://github.com/Evan1120/docker-elk) 搭建ELK服务，否则产生的日志将不会输出到logstash中。

Step1: 添加下面maven依赖
```
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
```

Step2: 配置logback.xml
```
<configuration debug="true">
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <springProperty scope="context" name="applicationName" source="spring.application.name"/>
    <springProperty scope="context" name="logstash-server" source="spring.logstash.server"/>
    <!-- Example for logging into the build folder of your project -->

    <appender name="logstash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <param name="Encoding" value="UTF-8"/>
        <remoteHost>${logstash-server}</remoteHost>
        <port>5000</port>
        <!-- encoder is required -->
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <customFields>{"applicationName":"${applicationName}"}</customFields>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="logstash"/>
    </root>

</configuration>
```

最终测试截图如下：

![images](https://github.com/Evan1120/springboot-logback-logstash-demo/blob/master/screenshorts/demo.png)
