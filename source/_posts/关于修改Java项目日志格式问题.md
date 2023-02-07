---
title: 关于Java配置自定义日志格式问题
date: 2023-02-03 12:52:36
tags: Java
---

### 常见的日志框架及修改方式

> 如果要修改项目的日志输出格式，首先需要明确，当前项目使用的是哪种日志输出框架。


#### Logback日志（spring项目默认）

1. 引入logback-spring.xml

   ```xml
   <configuration scan="false" scanPeriod="60 seconds" debug="false">
       <contextName>logback</contextName>
       <!--这里对应项目的基础配置文件-->
       <property resource="application.yml"/>
       <springProperty scope="context" name="serverName" source="spring.application.name"/>
   
       <!--配置自定义参数规则类的位置-->
       <conversionRule conversionWord="traceId" converterClass="cn.xxx.LogbackTradeConverterConfig" />
   
       <!--输出到控制台-->
       <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>INFO</level>
           </filter>
           <encoder>
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] tradeId:[%traceId] %-5level %logger{36} - %msg %n</pattern>
           </encoder>
       </appender>
   
       <appender name="file" class="ch.qos.logback.core.rolling.RollingFileAppender"><!-- 日志文件会滚动 -->
           <filter class="ch.qos.logback.classic.filter.LevelFilter">
               <level>INFO</level>
           </filter>
           <encoder>
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] tradeId:[%traceId] %-5level %logger{36} - %msg %n</pattern> <!-- 日志文件中日志的格式 -->
           </encoder>
       </appender>
   
       <root level="info">
           <appender-ref ref="console"/>
           <appender-ref ref="file"/>
       </root>
   </configuration>
   ```

2. application.yml中加入配置：

   ```yaml
   logging:
     config: classpath:logback-spring.xml
   ```

3. 自定义参数赋值配置 - 基础ClassicConverter

   ```java
   public class LogbackTradeConverterConfig extends ClassicConverter {
   
       @Override
       public String convert(ILoggingEvent iLoggingEvent) {
           // 返回skywalking追踪id
           return TraceContext.traceId();
       }
   
   }
   ```



#### Log4j

1. 修改项目日志框架为log4j2

   ```xml
   <!-- 日志框架改为使用log4j2 -->
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
   <dependency>
   ```

2. 引入配置文件 log4j2.yml

   其中%X{ip} 以及 %X{tradeId} 都是自定义参数

   ```yaml
   Appenders:
     Console:
       name: CONSOLE
       target: SYSTEM_OUT
       PatternLayout:
         pattern: "%d [%X{ip}] [%X{traceId}] %-5p %m [%t] (%c) (%F:%L) %n"
   
   Loggers:
     Root:
       level: info
       AppenderRef:
         - ref: CONSOLE
   ```

3. 修改application.yml 配置

   ```yaml
   logging:
     config: classpath:log4j2.yml
   ```

4. 自定义参数赋值 - 过滤器实现

   ```java
   @Configuration
   public class Log4jFilter implements Filter {
   
       @Override
       public void init(FilterConfig filterConfig) {
           // TODO Auto-generated method stub
       }
   
       @Trace
       @Override
       public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
               throws IOException, ServletException {
           String ip = request.getRemoteAddr();
           String traceId = TraceContext.traceId();
           MDC.put("ip", ip);
           MDC.put("traceId", traceId);
           chain.doFilter(request, response);
           MDC.remove("ip");
           MDC.remove("traceId");
       }
   
       @Override
       public void destroy() {
           // TODO Auto-generated method stub
       }
   
   }
   ```
