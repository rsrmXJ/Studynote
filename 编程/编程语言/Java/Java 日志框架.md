## Java 日志框架

##### 第一章 常见的 Java 日志框架与比较

1\. slf4j 日志门面

slf4j 是对所有日志框架制定的一种规范、标准、接口，并不是一个框架的具体的实现。**需要和具体的日志框架实现配合使用**（如log4j、logback）

2\. 日志实现

① log4j 是 apache 实现的一个开源、轻量级的日志框架

② logback 同样是由 log4j 的作者设计完成的，拥有更好的特性，用来取代 log4j 的一个日志框架，是 slf4j 的原生实现

③ Log4j2 是 log4j 1.x 和 logback 的改进版，据说采用了一些新技术（无锁异步、等等），使得日志的吞吐量、性能比log4j 1.x提高10倍，并解决了一些死锁的bug，而且配置更加简单灵活。

3\. 为什么需要日志门面 slf4j，直接使用具体的实现不就行了吗？

接口用于定制规范，可以有多个实现，使用时是面向接口的（导入的包都是slf4j的包而不是具体某个日志框架中的包），即直接和接口交互，不直接使用实现，所以可以任意的更换实现而不用更改代码中的日志相关代码。

比如：slf4j 定义了一套日志接口，项目中使用的日志框架是 logback，开发中调用的所有接口都是 slf4j的，不直接使用 logback，调用是自己的工程调用 slf4j 的接口，slf4j 的接口去调用 logback 的实现，可以看到整个过程应用程序并没有直接使用 logback，当项目需要更换更加优秀的日志框架时（如log4j2）只需要引入 Log4j2 的 jar 和 Log4j2 对应的配置文件即可，完全不用更改 Java 代码中的日志相关的代码 logger.info，也不用修改日志相关的类的导入的包

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
```

**使用日志门面 slf4j 便于更换为其他更加优良日志框架。**

**log4j、logback、log4j2都是一种日志具体实现框架，所以既可以单独使用也可以结合 slf4j 一起搭配使用**

##### 第二章  log4j

1\. 什么是  log4j ? 

log4j (log4j log for Java) 是一个开源、轻量级的、用于日志管理的框架。

> Log4j 是 Apache 一个开放源代码项目，通过使用 log4j 可以控制日志信息输送的目的地（控制台、文件、GUI 组件、甚至套接口服务器、NT的时间记录器等）。可以控制每一条日志信息的级别，能够更加细致控制日志的生成过程，通过一个配置文件来进行配置，而不需要修改应用的代码

2\. log4j 能干什么？

- 日志监控打印，在项目试运行期要记录用户的所有操作
- 添加新的内容（格式），比如时间、线程
- 程序调试期间，记录运行的步骤和运行行
- 成功上线稳定运行后，不需要打印
- 多个日志的输出源，比如到数据库，eclipse 控件

下载：www.logging.apache.org/

##### 第三章 log4j 的使用

**第一步** 在 pom.xml 添加 log4j 的依赖：

```xml
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    //如果使用 slf4j 标准则还需要添加 slf4j-log4j12 
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.30</version>
        <scope>test</scope>
    </dependency>
<dependencies>
```

**第二步** 创建 log4j.properties 文件：

log4j.properties 文件的位置：

```java
src/main/java/resources
在 properties 文件中
# 代表注释当前行
至少要配置两个输出源
```

log4j.properites 的四个关键词：

① 目的地 appender

② 布局 layout

③ 控制单元 logger

④ 级别 level

> propedit 插件使 properties 文件支持中文

**第三步** 配置根 Logger ，语法：`log4j.rootLogger=[level],appenderName1,appenderName2 …`

如果配置多个输出源，那么就是 **级别取精确，输出为各自**。例如：UserDAO 的完整路径为 com.atguigu.dao.UserDAO

```properties
log4j.rootLogger=warn,atguigu.file
log4j.logger.com.atguigu.dao=info,atguigu.Console,atguigu.File
log4j.logger.com.atguigu=error,atguigu.Console
```

那么 com.atguigu.dao 包中的所有日志信息的取的级别为 info。在这里那个 Logger 的命名最接近 com.atguigu.dao.UserDAO，那么该 Logger 就更为精确

> 如果 Appender 没有设置的对应的 Thresold，那么该 Appender 将以 rootLogger 指定的运行级别执行
>
> 如果 Appender 中没有定义级别，则以 logger 中定义为准。（如果都定义了那么取决于 Appender 中定义的级别）

*自定义输出源：*`log4j.logger.customName=[level],appenderName1,appenderName2…`

**第四步** 配置日志输出的目的地 Appender

log4j 提供的 appender 有:

① org.apache.log4j.ConsoleAppender 控制台、

② org.apache.log4j.FileAppender 文件、

③ org.apache.log4j.DailyRollingFileAppender 每天产生一个日志文件

④ org.apache.log4j.RollingFileAppender 文件大小到达指定尺寸产生一个新文件

⑤ org.apache.log4j.WriteAppender 把日志信息以流的形式发送到任意地方；WriterAppender 不能直接配置使用，使用更多的是前面四种

⑥ org.apache.log4j.jdbc.JDBCAppender 把日志用 JDBC 记录到数据库中

> appender 常用的命名格式: `公司名.类型` 例如：`huawei.Console`   

**第五步**   在 Java 程序中使用 log4j

```java
private static final Logger LOGGER = Logger.getLogger(当前类);//log4j
private static final Logger LOGGER = LoggerFactory.getLogger(当前类);//slf4j 前提条件需导入 slf4j-log4j12
//然后在程序使用 LOGGER.级别(需要打印的信息);
```

工作的 try-catch 写法

```java
try{
   logger.debug();
   代码
   logger.debug();
}catch(Exception e){
    e.printStackTrace(); //这一行代码可以不要
    logger.error(e.getMessage(),e.getCause());
    thorw new xxxxException;
} 
```

##### 第四章 log4j.properties 配置详解

1\. level

从低到高依次为 OFF(关闭)、DEBUG(调试)、INFO(信息)、WARN(警告)、ERROR(错误)、FATAL(重大错误，会直接导致程序中断)

包含关系：例如定义 level 为 INFO 级别，则大于及等于 INFO 级别的日志信息可以输出，而低于 INFO 级别的信息将无法输出。简而言之，一句话：**打印自身往后靠**

2\. 配置 appender 详解

① 配置 appender 语法

```
log4j.appender.appenderName = fully.qualified.name.of.appender.class
log4j.appender.appenderName.option1 = value1
…
log4j.apeender.appenderName.optionN = valueN
```

提供的 appender 及其参数 option:

**ConsoleAppender:**

- Thresold 默认为 DEBUG，指定日志输出的最低级别
- ImmediateFlush 默认为 TRUE，表示所有的信息都会被立即输出
- Target=System.out: 使用 System.out 在控制台输出，其它值：System.err

**FileAppender**

- Thresold 默认为 DEBUG，指定日志输出的最低级别
- ImmediateFlush 默认为 TRUE，表示所有的信息都会被立即输出
- File 指定消息输出的路径。例如：`File=E:\logs.txt` 将指定消息输出到 E 盘中的 logs.txt 文件
- Append 默认值为 true，即以追加的方式将消息追加到指定文件中，false 将消息覆盖指定文件的内容

**DailyRollingFileAppender**

- Thresold 默认为 debug，指定日志输出的最低级别
- ImmediateFlush 默认为 true，表示所有的信息都会被立即输出
- File 指定消息输出的路径。例如：`File=E:\logs.txt` 将指定消息输出到 E 盘中的 logs.txt 文件
- Append 默认值为 true，即以追加的方式将消息追加到指定文件中，false 将消息覆盖指定文件的内容
- DatePattern 用户可以指定按月、周、天、时、分产生新文件。例如：`DatePattern=.yyyy-ww` 每周产生一个新文件。用户可以指定按月、周、日、时、分滚动文件

**RollingFileAppender**

- Thresold 默认为 debug，指定日志输出的最低级别
- ImmediateFlush 默认为 true，表示所有的信息都会被立即输出
- File 指定消息输出的路径。例如：`File=E:\logs.txt` 将指定消息输出到 E 盘中的 logs.txt 文件
- Append 默认值为 true，即以追加的方式将消息追加到指定文件中，false 将消息覆盖指定文件的内容
- MaxFileSize 其值的后缀可以为 KB\MB\GB 在日志文件达到设置时，将会产生新文件，将原来的内容移动到 log.txt.1 中。
- MaxBackupIndex 指定可以产生的滚动文件的最大数(超过指定文件数，类似于链表对以前的内容进行覆盖)、

3\. layout 布局 

① 布局输出日志信息的格式

② 常见的 Layout

- org.apache.log4j.HTMLLayout： HTML 表格形式布局
- org.apache.log4j.PatternLayout： 自定义布局
- org.apache.log4j.SimpleLayout：包含日志的信息级别和信息字符串
- org.apache.log4j.TTCCLayout：包含日志的产生时间、线程、类别等信息

**HTMLayout** 的 option 包括如下：

- LocationInfo=true: 默认值为 false，输出 Java 文件名称和行号
- Title=Test_WARN: 默认值 LogrJ Log Messages

PatternLayout 的 option:

- ConversionPattern 以指定的信息格式输出

  - %p 优先级 例如：DEBUG、INFO、WARN、ERROR、FATAL
  - %r 输出自应用启动该 log 信息耗费的毫秒数
  - %c 所属的类目，通常就是所在类的全名
  - %n 回车符
  - %d 日志的时间点和时间日期，默认为 ISO8601，也可以指定其格式：例如：`%d{yyyy-MM-dd HH:mm:ss,SSS}` w 表示周
  - %l 日志时间的发生位置，包括类目名、发生线程、以及在代码的行数
  - %t 输出产生该日志的线程名
  - %msg 日志文本
  - %l: 输出日志事件的发生位置，相当于%C.%M(%F:%L)的组合,包括类目名、方法名，文件名、发生的线程，以及在代码中的行数。举例：Testlog4.main (TestLog4.java:10)
  - %x: 输出和当前线程相关联的NDC(嵌套诊断环境),尤其用到像java servlets这样的多客户多线程的应用中。
  -  %%: 输出一个"%"字符
  -  %F: 输出日志消息产生时所在的文件名称
  -  %L: 输出代码中的行号
  -  %m: 输出代码中指定的消息
  -  %M: 输出所在方法名
  -  %n: 输出一个回车换行符，Windows平台为"/r/n"，Unix平台为"/n"输出日志信息换行
  -  %C: 包名、类名
  
    可以在%与模式字符之间加上修饰符来控制其最小宽度、最大宽度、和文本的对齐方式。如：
  
   1) %20c：指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，默认的情况下右对齐。
   2) %-20c:指定输出category的名称，最小的宽度是20，如果category的名称小于20的话，"-"号指定左对齐。
   3) %.30c:指定输出category的名称，最大的宽度是30，如果category的名称大于30的话，就会将左边多出的字符截掉，但小于30的话也不会有空格。

常用信息格式：`%d{yyyy-MM-dd HH:mm:ss,SSS} %5p (%C.%M:%L)  - %m%n`

appender 举例：
``````xml         
log4j.appender.D.File = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File.file = E://JE_Project/logs/debug.log  
//日志输出路径 可更改
log4j.appender.D.File.DatePatttern=.yyyy-MM-dd
#log4j.appender.D.Append = true
#log4j.appender.D.Threshold = DEBUG
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss,SSS} %5p  [ %t:%r ] - [ %p ]  %m%n
# p 为优先级 priority
# 这里 D 应该为公司名
# 后面的 .File 也是 appender 名称，同理其它输出到控制台就是 .Console   
```

> log4j.properties?一句话
> 以什么样的格式，按照日志的优先级别，将日志输出到哪儿？

4\. properties 小总结：Appender、Layout、Logger 之间的关系：

① 每个 appender 后面必然需要跟随 layout,指定自己的风格样式

② 每个 Logger 都可以指定一个级别，同时引用多个 Appender

③ 每个 appender 也可以同时被多个 Logger 调用

##### 第五章 log4j 的另一种配置方式 xml

1\. 例子

```xml
<?xml version="1.0" encoding="UTF-8"?>       
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">       
          
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" >       
          
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
        \*这里也可以使用 level标签 例如：<level value="debug"></level>     *\
        <appender-ref ref="myConsole"/>       
        <appender-ref ref="myFile"/>          
    </root>       
</log4j:configuration>  
```

2\. xml 声明和 DTD 声明 
xml 配置文件的头部包括两个部分: xml 声明和 DTD 文档声明。头部的格式如下：

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">  
```

3\. log4j:configuration (root element)

- xmlns:log4j [#FIXED attribute] : 定义log4j的名字空间，取定值"http://jakarta.apache.org/log4j/"
- appender [? child] : 一个appender子元素定义一个日志输出目的地
- logger [? child] : 一个logger子元素定义一个日志控制单元
- root [? child] : root子元素定义了root logger

```xml
<log4j:configuration xmlns:log4j='http://jakarta.apache.org/log4j/' >
这里的 [? child] 代表子元素属于父子关系，而其他的皆为其属性
```

4\. appender
appender 元素定义一个日志输出目的地。

- name [#REQUIRED attribute] : 定义 appender 名字，以便被后文引用
- class [#REQUIRED attribute] : 定义 appender 对象所属的类的全名
- param [? child] : 创建 appender 对象时传递给类构造方法的参数
- layout [? child] : 该 appender 使用的 layout 对象

5\. layout 元素定义与某一个 appender 相联系的日志格式化器。

- class [#REQUIRED attribute] : 定义layout对象所属的类的全名
- param [* child] : 创建layout对象时传递给类构造方法的参数

6\. logger
logger元素定义一个日志输出器。

- name [#REQUIRED attribute] : 定义logger的名字，以便被后文引用
- additivity [#ENUM attribute] : 取值为"true"（默认）或者"false"，是否继承父logger的属性
- level 或 priority [? child] : 定义该 logger 的日志级别 
- appender-ref [* child] : 定义该 logger 的输出目的地 

7\. root

root 元素定义根日志输出器root logger。

- param [* child] : 创建 root logger 对象时传递给类构造方法的参数
- level [? child] : 定义 root logger 的日志级别
- appender-ref [* child] : 定义 root logger 的输出目的地

8\. level
level 元素定义 logger 对象的日志级别。 

- class [#IMPLIED attribute] : 定义 level 对象所属的类，默认情况下是"org.apache.log4j.Level类
- value [#REQUIRED attribute] : 为 level 对象赋值。可能的取值从小到大依次为 "all"、"debug"、"info"、"warn"、"error"、"fatal"和"off"。当值为"off"时表示没有任何日志信息被输出
- param [* child] : 创建 level 对象时传递给类构造方法的参数

9\. appender-ref

appender-ref 元素引用一个 appender 元素的名字，为 logger 对象增加一个appender。

- ref [#REQUIRED attribute] : 一个appender元素的名字的引用
- appender-ref元素没有子元素

10\. param
param 元素在创建对象时为类的构造方法提供参数。它可以成为 appender、layout、filter、errorHandler、level、categoryFactory 和 root 等元素的子元素。

- name and value [#REQUIRED attributes] : 提供参数的一组名值对
- param元素没有子元素

在 xml 文件中配置 appender 和 layout

创建不同的 Appender 对象或者不同的 Layout 对象要调用不同的构造方法。可以使用 param 子元素来设定不同的参数值。

11\. 举例创建各个 Appender 对象

**创建 ConsoleAppender 对象**

ConsoleAppender 的构造方法不接受其它的参数。

```xml
<appender name="console.log" class="org.apache.log4j.ConsoleAppender">  
  <layout ... >  
    ... ...  
  </layout>  
</appender>
```

**创建 FileAppender对象**

可以为 FileAppender 类的构造方法传递两个参数：File 表示日志文件名；Append 表示如文件已存在，是否把日志追加到文件尾部，可能取值为 "true" 和 "false"（默认）

```xml
<appender name="file.log" class="org.apache.log4j.FileAppender">  
  <param name="File" value="/tmp/log.txt" />  
  <param name="Append" value="false" />  
  <layout ... >  
    ... ...  
  </layout>  
</appender>  
```

**创建 RollingFileAppender 对象**

除了 File 和 Append 以外，还可以为 RollingFileAppender 类的构造方法传递两个参数：MaxBackupIndex 备份日志文件的个数（默认是1个）；MaxFileSize 表示日志文件允许的最大字节数（ 默认是10M）

```xml
<appender name="rollingFile.log" class="org.apache.log4j.RollingFileAppender">  
  <param name="File" value="/tmp/rollingLog.txt" />  
  <param name="Append" value="false" />  
  <param name="MaxBackupIndex" value="2" />  
  <param name="MaxFileSize" value="1024MB" />  
  <layout ... >  
    ... ...  
  </layout>  
</appender>
```

Tips: 如果 xml 和 properties 配置文件同时存在那么将会忽略 properties 配置文件。(xp)

**创建PatternLayout对象**

可以为 PatternLayout 类的构造方法传递参数 ConversionPattern。在 Appender 对象中创建

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

12\. 日志的传播机制

① 日志的可加性

一旦确定运行级别，那么

Logger 中的 additivity 属性

默认为 false,就是不让其他的 logger 随着输出

##### 第六章 Log4j2 的使用

1\. 在 pom.xml 中文件的配置。在 log4j2 中将 api 和 core 分离。在 pom.xml 文件中的配置如下：

```xml
<properties>
    <log4j-api.version>2.19.0</log4j-api.version>
    <log4j-core.version>2.19.0</log4j-core.version>
</properties>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>${log4j-api.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>${log4j-core.version}</version>
</dependency>
```

简单使用：

```
private static final Logger Logger = LogMannager.getLogger(${CLASSNAME}.class);//这里的参数放的是当前类
```

2\. 如果没有配置文件

① 工程中只引入的 jar 并没有引入任何配置文件，在测试的时候可以看到有 ERROR 输出：`“ERROR StatusLogger No log4j2 configuration file found. Using default configuration: logging only errors to the console.”`

② 输出 logger 时可以看到只有 error 和 fatal 级别的被输出来，是因为没有配置文件就使用默认的，默认级别是 error，所以只有 error 和 fatal 输出来 

③ 引入的包是log4j本身的包（import org.apache.logging.log4j.LogManager）

3\. 配置方式

2.x 版本不再支持像 1.x 中的 .properties 后缀的文件配置方式，2.x 版本常用 .xml 后缀的文件进行配置，除此之外还包含 .json 和 .jsn 配置文件、程序配置等

配置文件的位置：log4j2 默认会在 classpath 目录下寻找 log4j2.xml、log4j.json、log4j.jsn 等名称的文件，如果都没有找到，则会按默认配置输出，也就是输出到控制台（级别为 error），也可以对配置文件自定义位置（需要在 web.xml 中配置），一般放置在 src/main/resources 根目录下即可

配置文件自定义位置的两种方式:（一般不会使用）

```java
public static void main(String[] args) throws IOException {  
    File file = new File("D:/log4j2.xml");  
    BufferedInputStream in = new BufferedInputStream(new FileInputStream(file));  
    final ConfigurationSource source = new ConfigurationSource(in);  
    Configurator.initialize(null, source);  
      
    Logger logger = LogManager.getLogger("myLogger");  
} 
```

Web 工程的方式：
```xml
   <context-param>
       <param-name>log4jConfiguration</param-name>
       <param-value>/WEB-INF/log4j2.xml</param-value>
   </context-param> 
    <listener>
        <listener-class>org.apache.logging.log4j.web.Log4jServletContextListener</listener-class>
    </listener>
```

log4j2 虽然采用 xml 风格进行配置，依然包含三个组件,分别是 Logger(记录器)、Appender(输出目的地)、Layout(日志布局)。

##### 第七章 log4j2 xml 配置文件解析

1\. xml 声明

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

2\. 根节点 Configuration 有两个属性：status 和 monitorinterval,有两个字节点 Appenders 和 Loggers(表明可以定义多个Appender和Logger).

- status 用于指定 log4j 本身的打印日志级别。(用于控制log4j2日志框架本身的日志级别，如果将stratus设置为较低的级别就会看到很多关于log4j2本身的日志，如加载log4j2配置文件的路径等信息
- monitorinterval 为 log4j 新特点自动重载配置。指定自动重新配置的监测间隔时间，单位是 s,最小是 5s;含义是每隔多少秒重新读取配置文件，可以不重启应用的情况下修改配置

3\.  Appenders节点，常见的有三种子节点:Console、File、RollingFile

- Console 节点用来定义输出到控制台的 Appender.
- File 节点用来定义输出到指定位置的文件的 Appender.
- RollingRandomAccessFile节点用来定义超过指定大小自动删除旧的创建新的的 Appender.
  - fileName 指定当前日志文件的位置和文件名称

- properties: 属性
  使用来定义常量，以便在其他配置的时候引用，该配置是可选的，例如定义日志的存放位置
  D:/logs

4\. 常用的 Appender

① ConsoleAppender

参数:

| 名称   | 类型    | 描述                                                         |
| ------ | ------- | ------------------------------------------------------------ |
| filter | Fileter | 确定事件是否被该 Appender 处理。通过使用 CompositeFilter 可以使用多个 Filter |
| layout | Layout  | 确定日志信息的输出格式                                       |
| name   | String  | appdenr 的名称                                               |
| target | String  | 取值 "SYSTEM_OUT"（默认值） 或 “SYSTEM_ERROR”。如何输出日志信息 |

其他参数：看官方文档

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" name="MyApp" packages="">
  <Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
      <PatternLayout pattern="%m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="error">
      <AppenderRef ref="STDOUT"/>
    </Root>
  </Loggers>
</Configuration>
```

注：其他的 Appender 详看 `http://logging.apache.org/log4j/2.x/manual/appenders.html`

```
<PaternLayout pattern="%m%n">
等效于
<PatternLayout>
	<Pattern>%m%n</Pattern>
</PatternLayout>
```

② FileAppender

filter、layout、name 略

| 名称      | 类型    | 描述                                                         |
| --------- | ------- | ------------------------------------------------------------ |
| filanName | string  | 要写入的文件名称(包括目录)。如果其父目录或该文件不存在则创建该文件 |
| append    | boolean | true （默认值） 将日志信息添加到文件末尾                     |
|           |         |                                                              |
|           |         |                                                              |

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" name="MyApp" packages="">
  <Appenders>
    <File name="MyFile" fileName="logs/app.log">
      <PatternLayout>
        <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
      </PatternLayout>
    </File>
  </Appenders>
  <Loggers>
    <Root level="error">
      <AppenderRef ref="MyFile"/>
    </Root>
  </Loggers>
</Configuration>
```

③ RandomAccessFileAppender 与 FileAppnder 相似。但是效率更高

```xml
<RandomAccessFile name = “ MyFile” fileName = “ logs / app.log” >  
    <PatternLayout>
        <Pattern> ％d％p％c {1。} [％t]％m％n </ Pattern>
        </ PatternLayout>
    </ RandomAccessFile>
```

④ RollingFileAppender

filePattern  归档日志文件的文件名的模式。

```xml
<RollingFile name="RollingFile" filePattern="logs/app-%d{yyyy-MM-dd-HH}-%i.log.gz">
    <PatternLayout>
        <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
    </PatternLayout>
    <Policies>
        <CronTriggeringPolicy schedule="0 0 * * * ?"/>
        <SizeBasedTriggeringPolicy size="250 MB"/>
    </Policies>
    <DirectWriteRolloverStrategy maxFiles="10"/>
</RollingFile>
```

多重出发策略：

```xml
<Policies>
  <OnStartupTriggeringPolicy />
  <SizeBasedTriggeringPolicy size="20 MB" />
  <TimeBasedTriggeringPolicy />
</Policies>
//当日志文件到达 20 M 以及当前日期与日志的开始日期不匹配时。对日志进行滚动。
```

日志存档保留策略：过度是删除

```xml
      <DefaultRolloverStrategy>
        <Delete basePath="${baseDir}" maxDepth="2">
          <IfFileName glob="*/app-*.log.gz" />
          <IfLastModified age="60d" />
        </Delete>
      </DefaultRolloverStrategy>
//基本目录下与“ * / app-*。log.gz” g​​lob匹配且已存在60天或更早的所有文件都将在过渡时删除。
```

删除参数

| 参数名称      | 类型            | 描述                                                         |
| ------------- | --------------- | ------------------------------------------------------------ |
| basePath      | String          | 要删除文件所在的路径                                         |
| maxDepth      | int             | 要访问目录的最大级别数。值为 0 表示仅访问起始文件（基本路径本身），除非安全管理器拒绝。Integer.MAX_VALUE 的值指示应访问所有级别。默认值为 1，表示仅访问指定 basePath 目录中的文件。 |
| testMode      | boolean         | true。不删除文件。而是在 INFO 级别下达将以效益打印到状态记录器。默认为 false |
| pathSorter    | 路径排序        | 在选择要删除的文件之前对文件进行排序。默认                   |
| pathCondition | PathCondition[] |                                                              |
|               |                 |                                                              |
|               |                 |                                                              |
|               |                 |                                                              |

4\. Filter

① CompositeFilter 复合过滤器。

提供了指定多个过滤其的方法

```xml
  <Filters>
    <MarkerFilter marker="EVENT" onMatch="ACCEPT" onMismatch="NEUTRAL"/>
    <DynamicThresholdFilter key="loginId" defaultThreshold="ERROR"
                            onMatch="ACCEPT" onMismatch="NEUTRAL">
      <KeyValuePair key="User1" value="DEBUG"/>
    </DynamicThresholdFilter>
  </Filters>
```

② ThresholdFilter 

参数

| 参数名称   | 类型   | 描述                                                         |
| ---------- | ------ | ------------------------------------------------------------ |
| level      | String | 匹配级别                                                     |
| onMath     | String | 过滤器匹配时采取的措施。取值 ACCEPT 接受、DENY 拒绝、NEUTRAL。默认为中性 |
| onMismatch | String | 过滤器不匹配时采取的措施。默认为拒绝                         |
|            |        |                                                              |

`<ThresholdFilter level="TRACE" onMatch="ACCEPT" onMismatch="DENY"/>`

注：多个配合使用需要使用复合过滤器

其他请看官网文档

5\. additivity

```xml
<Logger name="com.foo.Bar" level="trace" additivity="false">
    <AppenderRef ref="Console"/>
</Logger>
```

6\. 将 log4j2 升级为 slf4j 标准

```xml
//在 pom.xml 中引入 log4j-slf4j-impl
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.14.0</version>
</dependency>
```

![image-20210203232645114](image-20210203232645114.png)

##### 第八章 日志规范

###### 1\. 规范

1\. 项目中不要直接使用日志框架中的 API, 而要使用 SLF4J 门面中的 API。使用门面模式的日志框架，有利于为何和各个类的日志处理方式统一。

2\. 日志文件至少保存15天，因为有些异常具备以“周”为频次发生的特点。

3\. 对 trace/debug/info 级别的输出必须使用条件输出的形式或使用占位符的形式

4\. 异常信息应该包括狼类星系，案发现场信息，和异常堆栈信息。

```
也就是上面 try-catch 语句中的：`LOGGER.ERROR(e.toString+"_"+e.getMessage,e);`
```

5\. 尽量使用英文描述日志，防止出现中文乱码的问题，当使用英文描述不清时，再使用中文描述

###### 2\. 日志输出文件的存储路径格式规范

①  日志文件统一存放在用户目录下的 logs 文件夹中，根据应用名称生成文件夹进行区分，如图2-1 所示。单项目多应用情况下，可根据实际情况修改 log4.xml 中的路径（依然存放在 logs 目录下）

```
~/logs/应用名称/日志
```

② 存放日志输出文件路径

日志文件统一存放在用户目录下的ogs文件夹中，根据应用名称生成文件夹进行区分，然后以年月 “yyy-MM” 生成文件夹，以日志级别（如info/warn/ error等）或日志类型（如 stats/ monitor/ access等）生成文件夹

```xml
~/logs/应用名称/yyyy-MM/存档日志
```

