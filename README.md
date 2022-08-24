# springboot-logback-logstash-demo
How does the logback transfer the log to logstash? Try this demo

在运行本示例之前，请参考[docker-elk](https://github.com/Evan1120/docker-elk) 搭建ELK服务，否则产生的日志将不会输出到logstash中。

Step1: 添加下面maven依赖
```xml
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>net.logstash.logback</groupId>
            <artifactId>logstash-logback-encoder</artifactId>
            <version>4.11</version>
        </dependency>
```

Step2: 配置logback.xml
```xml
<configuration debug="true">
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <springProperty scope="context" name="applicationName" source="spring.application.name"/>
    
    <springProperty scope="context" name="logstash-host" source="elk.logstash.server"/>
    <springProperty scope="context" name="logstash-port" source="elk.logstash.port"/>
    <springProperty scope="context" name="elkEnabled" source="elk.enable"/>
    <!-- Example for logging into the build folder of your project -->

    <if condition='property("elkEnabled").contains("true")'>
        <then>
            <appender name="logstash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
                <param name="Encoding" value="UTF-8"/>
                <remoteHost>${logstash-host}</remoteHost>
                <port>${logstash-port}</port>
                <!-- encoder is required -->
                <encoder class="net.logstash.logback.encoder.LogstashEncoder">
                    <customFields>{"applicationName":"${applicationName}","env":"${env}"}</customFields>
                </encoder>
            </appender>
        </then>
    </if>

    <root level="INFO">
        <appender-ref ref="logstash"/>
    </root>

</configuration>
```

最终测试截图如下 :blush:

![images](https://github.com/CrayfishGo/springboot-logback-logstash-demo/blob/master/screenshorts/demo.png)
