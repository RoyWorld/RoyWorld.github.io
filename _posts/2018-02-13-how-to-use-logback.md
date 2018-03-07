---
layout: post
title:  "How To Use Logback"
date:   2018-02-13 16:32:39
category: framework
published: true
author: RoyChan
tags:
- java
- framework
- log
---

# about log framework
当前常用的log framework无非就是logback和log4j. 而由于log4j的可配置度低和功能较少, 所以实际上会用的比较少, 而更多的是使用logback作为log framework. Therefore, 这篇paper将写关于how to use logback.

# basic concern
### what is log
实质上, log是一个输出program各种信息的工具, 可以说存在于任何program中, 而不同的仅仅是其展示形式, 而常见的展示形式有: **console out、file、数据库记录、页面弹出窗**等等, 其中console out是最为常见的形式.

### log level
log level可以说是log framework中最为重要的concern. why? 原因是log level如下作用:
1. 设置log level有助于区分log的类别和重要性, 并以此筛选有用的信息.
2. 通过控制log level来控制输出的log信息. 
3. 由于输出了program的runtime信息, 所以对program的监控和调试都很有帮助.

log level可参考下图:

![log_level](https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180213/20180213_log_level.png)

### the form of log
 log的形式实质上是可以多种多样的, 但运用logback最常使用的log形式有两种, 分别是:
 * file log, 将log输出到指定的文件中
 * console log, 将log输出到console中
 
# about logback
关于logback的使用, 这里仅做简单的记录, 更详细的说明就参考这篇[paper][R1]

### logback configuration files
There are three valid standard file names you can choose from:
* logback-test.xml
* logback.groovy
* logback.xml

### appenders
> appenders are the elements responsible for writing log statements. All appenders must implement the Appender interface.
> 
> Furthermore, each appender corresponds to a certain type of output or mode of sending data. Here are some of the most helpful appenders you can configure:
> * ConsoleAppender – writes messages to the system console
> * FileAppender – appends messages to a file
> * RollingFileAppender – extends the FileAppender with the ability to roll over log files
> * SMTPAppender – sends log messages in an email, by default only for ERROR messages
> * DBAppender – adds log events to a database
> * SiftingAppender – separates logs based on a runtime attribute

上述是logback提供的`appender`, 实质上只需要实现`Appender interface`即可做`custom appender`, 示例如下:
[java code][R4], xml配置:
```xml
<appender name="DEBUG-FILE-APPENDER" class="com.trasher.util.CountingFileAppender">
        <limit>100</limit>
        <file>${user.dir}/logs/debug.log</file>
        <encoder>
            <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n
            </pattern>
        </encoder>
        <append>true</append>

    </appender>
```


### layouts and encoders
> The components responsible for transforming a log message to the desired output format are layouts and encoders.
>
> Layouts can only transform a message into String, while encoders are more flexible and can transform the message into a byte array, then write that to an OutputStream. This means encoders have more control over when and how bytes are written.
>
> Some of the most commonly used layouts are PatternLayout, HTMLLayout and XMLLayout

### loggers
> Loggers are the third main component of Logback, which developers can use to log messages at a certain level.

configuration中的两种logger:
* root logger:

```xml
<root level="debug">
    <appender-ref ref="STDOUT" />
</root>
```
This sits at the top of the logger hierarchy and is provided by default, even if you don’t configure it explicitly, with a ConsoleAppender with the DEBUG level.
* normal logger. 

```xml
<logger level="info" name="rollingFileLogger">
    <appender-ref ref="rollingFileAppender" />
</logger>
```

If you don’t explicitly define a log level, the logger will inherit the level of its closest ancestor; in this case, the DEBUG level of the root logger.


### logger additivity
By default, a log message will be displayed by the logger which writes it, as well as the ancestor loggers. And, since root is the ancestor of all loggers, all messages will also be displayed by the root logger.

To disable this behavior, you need to set the additivity=false property on the logger element:
```xml
<logger level="info" name="rollingFileLogger" additivity=false>
   ...
</logger>
```
### filtering logs
> Logback has solid support for additional filtering, beyond just the log level, This is done with the help of filters – which determine whether a log message should be displayed or not.

三种不同的FilterReply:
* DENY. The DENY value indicates the log event will not be processed 
* ACCEPT. ACCEPT means the log event is processed, skipping the evaluation of the remaining filters.
* NEUTRAL. NEUTRAL allows the next filters in the chain to be evaluated. If there are no more filters, the message is logged.

与log level有关的filter: `LevelFilter`和`ThresholdFilter`

对event做评估来决定是否输出log: `GEventEvaluator`和`JaninoEventEvalutor`

### extending logback
> For example, here are several ways you can extend Logback’s functionality:
> 
> * create a custom appender by extending the AppenderBase class and implementing the append() method
> * create a custom layout by subclassing the LayoutBase class and defining a doLayout() method
> * create a custom filter by extending the Filter class and implementing the decide() method
> * create a custom TurboFilter by extending the TurboFilter class and overriding the decide() method

# references
- [https://stackify.com/logging-logback/][R1]
- [https://www.mkyong.com/logging/logback-xml-example/][R2]
- [https://logback.qos.ch/manual/introduction.html][R3]

[R1]: https://stackify.com/logging-logback/
[R2]: https://www.mkyong.com/logging/logback-xml-example/
[R3]: https://logback.qos.ch/manual/introduction.html
[R4]: https://raw.githubusercontent.com/RoyWorld/RoyWorld.github.io/master/images/20180213/20180213_custom_appender.java
