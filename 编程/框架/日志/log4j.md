# log4j

### 1\. 什么是  log4j ? 

log4j (log4j log for Java) 是一个开源、轻量级的、用于日志管理的框架。

Log4j 是 Apache 一个开放源代码项目，通过使用 log4j 可以控制日志信息输送的目的地（控制台、文件、GUI 组件、甚至套接口服务器、NT的时间记录器等）。可以控制每一条日志信息的级别，能够更加细致控制日志的生成过程，通过一个配置文件来进行配置，而不需要修改应用的代码

log4j 能干什么？

- 日志监控打印，在项目试运行时期要记录用户的所有操作
- 添加新的内容，比如时间、线程
- 程序调试期间，记录运行的步骤和运行行
- 成功上线稳定运行后，不需要打印
- 多个日志的输出源，比如到数据库，eclipse 控件

下载：www.logging.apache.org/

### 2. log4j1 的配置 

lo4j.properties 

```java
log4j.appender.D.File = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File.file = E://JE_Project/logs/debug.log           //日志输出路径 可更改
log4j.appender.D.File.DatePatttern=.yyyy-MM-dd
#log4j.appender.D.Append = true
#log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss,SSS} %5p  [ %t:%r ] - [ %p ]  %m%n
`log4j.rootLogger=[level],appenderName1,appenderName2 
# p 为优先级 priority
# 这里 D 应该为公司名
# 后面的 .File 也是 appender 名称，同理其它输出到控制台就是 .Console   
```

`#` 表示注释

**配置根 rootLogger**

`log4j.rootLogger=[level],appenderName1,appenderName2 …`

如果配置多个输出源，那么就是 **级别取精确**，输出为各自

>  如果 Appender 没有设置的对应的 Thresold，那么该 Appender 将以 rootLogger 指定的运行级别执行

### 3\. level 日志级别*

从低到高依次为 OFF(关闭)、DEBUG(调试)、INFO(信息)、WARN(警告)、ERROR(错误)、FATAL(重大错误，会直接导致程序中断)

包含关系：例如定义 level 为 INFO 级别，则大于及等于 INFO 级别的日志信息可以输出，而低于 INFO 级别的信息将无法输出。简而言之，一句话：打印自身往后靠

> 什么是 log4j?一句话
> 以什么样的格式，按照日志的优先级别，将日志输出到哪儿？

### 4\. Appender

*appendName* 日志的输出名称

**配置日志的输出目的地 appender**

```
log4j.appender.appenderName = fully.qualified.name.of.appender.class
log4j.appender.appenderName.option1 = value1
…
log4j.apeender.appenderName.optionN = valueN
```

> log4j 提供的 appender 有 ConsoleAppender 控制台、FileAppender 文件、DailyRollingFileAppender 每天产生一个日志文件、RollingFileAppender 文件大小到达指定尺寸产生一个新文件和 WriteAppender。<br/>
> WriterAppender 不能直接配置使用，使用更多的是前面四种

具体使用:

例如：`org.apache.log4j.ConsoleAppender`

*option*

共有的 option :

- Thresold 默认为 DEBUG，指定日志输出的最低级别
- ImmediateFlush 默认为 TRUE，表示所有的信息都会被立即输出

tips: 我这里说的共有是都有这个 option，并不是共用，每个 appender 都有一套独立的 option

**ConsoleAppender:**

- Target=System.out: 使用 System.out 在控制台输出，其它值：System.err

**FileAppender**

- File  指定消息输出的路径。例如：`File=E:\logs.txt` 将指定消息输出到 E 盘中的 logs.txt 文件
- Append 默认值为 true，即以追加的方式将消息增加到指定文件中，false 将消息覆盖指定文件的内容

**DailyRollingFileAppender**

- File、Append ：略
- DatePattern 用户可以指定按月、周、天、时、分产生新文件。例如：`DatePattern=.yyyy-ww` 每周产生一个新文件。用户可以指定按月、周、日、时、分滚动文件

**RollingFileAppender**

- File、Append：略
- MaxFileSize 其值的后缀可以为 KB\MB\GB 在日志文件达到设置时，将会产生新文件，将原来的内容移动到 log.txt.1 中。
- MaxBackupIndex  指定可以产生的滚动文件的最大数(超过指定文件数，类似于链表对以前的内容进行覆盖)、

*WriterAppender* 

把日志信息以流的形式发送到任意地方

*JDBCAppender* 把日志用 JDBC 记录到数据库中

### 5\. layout 

输出信息的格式

- HTMLLayout： HTML形式布局
- PatternLayout： 自定义布局
- SimpleLayout：包含日志的信息级别和信息字符串
- TTCCLayout：包含日志的产生时间、线程、类别等信息

例如：`org.apache.logj.PatternLayout`

##### PatternLayout

PatternLayout 的 option 包括如下：

- locationInfo  true 输出 java 文件名称和行号。默认值为false

- Title 默认值为 Log4j Log Messages。其他值 Test_WARN

- ConversionPattern 
  
  - %p 优先级 例如：DEBUG、INFO、WARN、ERROR、FATAL
  - %r 输出自应用启动该 log 信息耗费的毫秒数
  - %c 所属的类目，通常就是所在类的全名
  - %n 回车符
  - %d 日志的时间点和时间日期，默认为 ISO8601，也可以指定其格式：例如：`%d{yyyy-MM-dd HH:mm:ss,SSS}`
  - %l 日志时间的发生位置，包括类目名、发生线程、以及在代码的行数
  - %t 输出产生该日志的线程名
  - %msg 日志文本
  - %l: 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、方法名，文件名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10)
  - %x: 输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像java servlets这样的多客户多线程的应用中。
  -  %%: 输出一个"%"字符
  -  %F: 输出日志消息产生时所在的文件名称
  -  %L: 输出代码中的行号
  -  %m: 输出代码中指定的消息,产生的日志具体信息
  -  %M: 输出所在方法名
  -  %n: 输出一个回车换行符，Windows平台为"/r/n"，Unix平台为"/n"输出日志信息换行
  -  %C: 包名、类名
  
    可以在%与模式字符之间加上修饰符来控制其最小宽度、最大宽度、和文本的对齐方式。如：
  
   1) %20c：指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，默认的情况下右对齐。
   2) %-20c:指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，"-"号指定左对齐。
   3) %.30c:指定输出category的名称，最大的宽度是30，如果category的名称大于30的话，就会将左边多出的字符截掉，但小于30的话也不会有空格。

使用 HTML 布局：(输出为 HTML)

```xml
log4j.appender.appenderName.layout=org.apache.log4j.HTMLLayout
log4j.appender.appenderName.layout.Title=HTML Layout Example /标题 默认为 Log4J Log Messages
log4j.appender.appenderName.layout.LocationInfo=true
```


在程序中的使用：

```java
package com.atguigu.******;
import org.apache.log4j.Logger;

public class TestLog4j{
    public static final Logger logger = Logger.getLogger(TestLog4j.class);//log4j
    public static final Logger logger1 = LoggerFactory.getLogger(TestLog4j.class);//slf4j
    public static void main(String[] args){
        logger.debug("debug level");
        ……
        logger.error("error level");
    }
}
```

File

properties 文件对中文支持不好


### 3. log4j2 使用 xml 进行配置

2.10.*　之后

Maven 工程将配置文件放到 resources 文件夹下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN" monitorInterval="300">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] $5p %-5level %logger{36} - %msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="mylog" level="trace" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Root level="DEBUG">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```

### 4. appender、layout 配置

slf4j

```java
private static final Logger logger = LoggerFactory.getLogger(this.getClass());//slf4j
//private static final Logger logger = Logger.getLogger(this.getClass());
private static final Logger logger = LogManager.getLogger();

```

常用的 try-catch 写法

```java
trt{
   logger.debug();
   代码
   logger.debug();
}catch(Exception e){
    e.printStackTrace(); //这一行代码可以不要
    logger.error(e.getMessage(),e.getCause());
    thorw new xxxxException;
}
```

两个输出源：级别取精确，输出为各自

tips: 当有多个 logger，那么就需确定一个输出级别。

例如：`com.atguigu.dao 包下的 UserDao`

```java
private static final Logger logger = Logger.getLogger(UserDao.class);
```

```xml
log4j.rootLogger=warn,atguigu.file
log4j.logger.com.atguigu.dao=info,atguigu.Console,atguigu.File
log4j.logger.com.atguigu=error,atguigu.Console
```
tips:这里只可以趋近于 UserDao，而不能是 UserDao

properties 小总结：Appender、Layout、Logger 之间的关系：

1. 每个 appender 后面必然需要跟随 layout,指定自己的风格样式
2. 每个 Logger 都可以指定一个级别，同时引用多个 Appender
3. 每个 appender 也可以同时被多个 Logger 调用

**log4j.xml**

日志记录的优先级 `priority`

```xml
<?xml version="1.0" encoding="UTF-8"?>       
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">       
          
<log4j:configuration xmlns:log4j='http://jakarta.apache.org/log4j/' >       
          
    <appender name="myConsole" class="org.apache.log4j.ConsoleAppender">       
        <layout class="org.apache.log4j.PatternLayout">       
            <param name="ConversionPattern"          
                value="[%d{dd HH:mm:ss,SSS\} %-5p] [%t] %c{2\} - %m%n" />       
        </layout>       
        <!--过滤器设置输出的级别-->       
        <filter class="org.apache.log4j.varia.LevelRangeFilter">       
            <param name="levelMin" value="debug" />       
            <param name="levelMax" value="warn" />       
            <param name="AcceptOnMatch" value="true" />       
        </filter>       
    </appender>       
       
    <appender name="myFile" class="org.apache.log4j.RollingFileAppender">          
        <param name="File" value="D:/output.log" /><!-- 设置日志输出文件名 -->       
        <!-- 设置是否在重新启动服务时，在原有日志的基础添加新日志 -->       
        <param name="Append" value="true" />       
        <param name="MaxBackupIndex" value="10" />       
        <layout class="org.apache.log4j.PatternLayout">       
            <param name="ConversionPattern" value="%p (%c:%L)- %m%n" />       
        </layout>       
    </appender>       
         
    <appender name="activexAppender" class="org.apache.log4j.DailyRollingFileAppender">       
        <param name="File" value="E:/activex.log" />         
        <param name="DatePattern" value="'.'yyyy-MM-dd'.log'" />         
        <layout class="org.apache.log4j.PatternLayout">       
         <param name="ConversionPattern"         
            value="[%d{MMdd HH:mm:ss SSS\} %-5p] [%t] %c{3\} - %m%n" />       
        </layout>         
    </appender>       
          
    <!-- 指定logger的设置，additivity指示是否遵循缺省的继承机制-->       
    <logger name="com.runway.bssp.activeXdemo" additivity="false">       
        <priority value ="info"/>         
        <appender-ref ref="activexAppender" />         
    </logger>       
       
    <!-- 根logger的设置-->       
    <root>       
        <priority value ="debug"/>       
        <appender-ref ref="myConsole"/>       
        <appender-ref ref="myFile"/>          
    </root>       
</log4j:configuration>  
```

log4j.xml配置文件节点详解

**1. xml声明和DTD声明**
xml配置文件的头部包括两个部分: xml声明和DTD声明。头部的格式如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">  
```

log4j:configuration (root element)
- xmlns:log4j [#FIXED attribute] : 定义log4j的名字空间，取定值"http://jakarta.apache.org/log4j/"
- appender [* child] : 一个appender子元素定义一个日志输出目的地
- logger [* child] : 一个logger子元素定义一个日志控制单元
- root [? child] : root子元素定义了root logger

`<log4j:configuration xmlns:log4j='http://jakarta.apache.org/log4j/' >`

appender
appender元素定义一个日志输出目的地。

- name [#REQUIRED attribute] : 定义appender的名字，以便被后文引用
- class [#REQUIRED attribute] : 定义appender对象所属的类的全名
- param [* child] : 创建appender对象时传递给类构造方法的参数
- layout [? child] : 该appender使用的layout对象

layout元素定义与某一个appender相联系的日志格式化器。

- class [#REQUIRED attribute] : 定义layout对象所属的类的全名
- param [* child] : 创建layout对象时传递给类构造方法的参数

logger
logger元素定义一个日志输出器。

- name [#REQUIRED attribute] : 定义logger的名字，以便被后文引用
- additivity [#ENUM attribute] : 取值为"true"（默认）或者"false"，是否继承父logger的属性
- level [? child] : 定义该logger的日志级别
- appender-ref [* child] : 定义该logger的输出目的地

root
root元素定义根日志输出器root logger。

param [* child] : 创建root logger对象时传递给类构造方法的参数
level [? child] : 定义root logger的日志级别
appender-ref [* child] : 定义root logger的输出目的地


level
level元素定义logger对象的日志级别。 

- class [#IMPLIED attribute] : 定义level对象所属的类，默认情况下是"org.apache.log4j.Level类
- value [#REQUIRED attribute] : 为level对象赋值。可能的取值从小到大依次为"all"、"debug"、"info"、"warn"、"error"、"fatal"和"off"。当值为"off"时表示没有任何日志信息被输出
- param [* child] : 创建level对象时传递给类构造方法的参数


appender-ref

appender-ref元素引用一个appender元素的名字，为logger对象增加一个appender。

- ref [#REQUIRED attribute] : 一个appender元素的名字的引用
- appender-ref元素没有子元素

param
param元素在创建对象时为类的构造方法提供参数。它可以成为appender、layout、filter、errorHandler、level、categoryFactory和root等元素的子元素。

- name and value [#REQUIRED attributes] : 提供参数的一组名值对
- param元素没有子元素

在xml文件中配置appender和layout

创建不同的Appender对象或者不同的Layout对象要调用不同的构造方法。可以使用param子元素来设定不同的参数值。

创建ConsoleAppender对象

ConsoleAppender的构造方法不接受其它的参数。

```xml
<appender name="console.log" class="org.apache.log4j.ConsoleAppender">  
  <layout ... >  
    ... ...  
  </layout>  
</appender>
```

**创建FileAppender对象**

可以为FileAppender类的构造方法传递两个参数：File表示日志文件名；Append表示如文件已存在，是否把日志追加到文件尾部，可能取值为"true"和"false"（默认）

```xml
<appender name="file.log" class="org.apache.log4j.FileAppender">  
  <param name="File" value="/tmp/log.txt" />  
  <param name="Append" value="false" />  
  <layout ... >  
    ... ...  
  </layout>  
</appender>  
```

**创建RollingFileAppender对象**

除了File和Append以外，还可以为RollingFileAppender类的构造方法传递两个参数：MaxBackupIndex备份日志文件的个数（默认是1个）；MaxFileSize表示日志文件允许的最大字节数（默认是10M）

```xml
<appender name="rollingFile.log" class="org.apache.log4j.RollingFileAppender">  
  <param name="File" value="/tmp/rollingLog.txt" />  
  <param name="Append" value="false" />  
  <param name="MaxBackupIndex" value="2" />  
  <param name="MaxFileSize" value="1024" />  
  <layout ... >  
    ... ...  
  </layout>  
</appender>
```

Tips: 如果 xml 和 properties 配置文件同时存在那么将会忽略 properties 配置文件。(xp)


**创建PatternLayout对象**

可以为PatternLayout类的构造方法传递参数ConversionPattern。

```xml
<layout class="org.apache.log4j.PatternLayout>  
  <param name="Conversion" value="%d [%t] %p - %m%n" />  
</layout>  
```

使用<filter> 精确匹配，避免往后靠的大于等于，过滤出所需要的

```xml
 <!--过滤器设置输出的级别-->       
        <filter class="org.apache.log4j.varia.LevelRangeFilter" additivity="false">       
            <param name="levelMin" value="debug" />       
            <param name="levelMax" value="warn" />       
            <param name="AcceptOnMatch" value="true" />       
        </filter>   
```

日志的传播机制

additivity。默认为 falsse,就是不让其他的 logger 随着输出 精确匹配

log4e 

- 官网:http://log4e.jayefem.de/

为什么这样写？

1 我们在看一些成熟框架代码中，经常会看到如下代码：

```java
if(logger.isDebugEnabled()){
    logger.debug("debug:"+name);
    
}
```

log4e 中也出现大量类似于上面的代码。

问题：为什么不直接写 `logger.debug("debug:"+name);`

A： 在配置文件中虽然可以使用控制级别比 debug 更高的级别，而不输出 debug 信息，但这里的字符串连接人会影响执行效率。(会新建一个String对象在常量池中)：因此如果向判断 logger 的级别，如果级别不合适，那么字符串的拼接就可以不用做了，从而可以提升效率。

缺点：可读性差，后期维护影响体验。