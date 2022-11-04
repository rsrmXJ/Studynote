# Maven

### 1. Maven 核心程序的安装与环境配置

① Maven 是使用 Java 开发的，因此我们首先需要确保安装了 JDK。

在 cmd 窗口中检查 `Java_HOME` 变量：`echo %JAVA_HOME%`

② 解压 Maven 核心程序的压缩包，放在一个非中文无空格的环境下

> Maven 下载地址 : `https://maven.apache.org/download.cgi`

③ 配置 Maven 的环境变量

- Maven 根目录变量名为 M2_HOME 或  MAVEN_HOME
- 增加 Path  的变量值：`%M2_HOME%\bin`

tips: 为了更好的兼容性，推荐使用 M2_HOME

④ 验证: 在 cmd 窗口中使用 `mvn -v` 命令

```
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: D:\devkit\apache-maven-3.6.3\bin\..
Java version: 15.0.1, vendor: Oracle Corporation, runtime: D:\devkit\jdk-15.0.1
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

### 2. maven why

> 导言：生产环境下开发不再是一个项目一个工程，而是每一个模块创建一个工程，而多个模块整合在一起就需要使用到像 Maven 这样的构建工具。

1\. 真的需要用 Maven 吗？Maven 是干什么用的？

这是很多同学在刚开始接触 Maven 时最大的问题。之所以会提出这个问题，是因为即使不使用 Maven 我们仍然可以进行 B/S 结构项目的开发。从表述层(展现层、控制层)、业务逻辑层到持久化层再到数据库都有成熟的解决方案——不使用 Maven 我们一样可以开发项目啊？

>- 展示层：H5、CCS3、JavaScript、jQuery
>- 控制层: SpringMVC、strusts2
>- 业务逻辑层： Spring IOC、Spring AOP
>- 持久化层：JDBC、Hibernate、MyBatis…
>- 数据库：MySQL、Oracle、DB2…

这里给大家纠正一个误区，Maven 并不是直接用来辅助编码的，它战斗的岗位并不是以上各层。所以我们有必要通过企业开发中的实际需求来看一看哪些方面是我们现有技术的不足。

2\. 为什么要使用 Maven?它能帮助我们解决什么问题？

① 添加第三方 jar 包

在今天的 JavaEE 开发领域，有大量的第三方框架和工具可以供我们使用。要使用这些 jar 包最简单的方法就是复制粘贴到 WEB-INF/lib 目录下。但是这会导致每次创建一个新的工程就需要将 jar 包复制到 lib 目录下，从而造成工作区中存在大量重复的文件，让我们的工程显得很臃肿。

而使用 Maven 后每个 jar 包本身只在本地仓库中保存一份，需要 jar 包的工程只需要以坐标的方式简单的引用一下就可以了。不仅极大的节约了存储空间，让项目更轻巧，更避免了重复文件太多而造成的混乱。

② jar 包之间的依赖关系

jar 包往往不是孤立存在的，很多 jar 包都需要在其他 jar 包的支持下才能够正常工作，我们称之为 **jar 包之间的依赖关系**。最典型的例子是：commons-fileupload-1.3.jar 依赖于 commons-io-2.0.1.jar，如果
没有 IO 包，FileUpload 包就不能正常工作。

那么问题来了，你知道你所使用的所有 jar 包的依赖关系吗？当你拿到一个新的从未使用过的 jar 包，你如何得知他需要哪些 jar 包的支持呢？如果不了解这个情况，导入的 jar 包不够，那么现有的程序将不能正常工作。再进一步，当你的项目中需要用到上百个 jar 包时，你还会人为的，手工的逐一确认它们依赖的其他 jar 包吗？这简直是不可想象的。而引入 Maven 后，Maven 就可以替我们自动的将当前 jar 包所依赖的其他所有 jar 包全部导入进来，无需人工参与，节约了我们大量的时间和精力。用实际例子来说明就是：通过 Maven 导入 commons-fileupload-1.3.jar 后，commons-io-2.0.1.jar 会被自动导入，程序员不必了解这个依赖关系。

③ 获取第三方 jar 包

JavaEE 开发中需要使用到的 jar 包种类繁多，几乎每个 jar 包在其本身的官网上的获取方式都不尽相同。为了查找一个 jar 包找遍互联网，身心俱疲，没有经历过的人或许体会不到这种折磨。不仅如此，费劲心血找的 jar 包里有的时候并没有你需要的那个类，又或者有同名的类没有你要的方法——以不规范的方式获取的 jar 包也往往是不规范的。

使用 Maven 我们可以享受到一个完全统一规范的 jar 包管理体系。你只需要在你的项目中以坐标的方式依赖一个 jar 包，Maven 就会自动从中央仓库进行下载，并同时下载这个 jar 包所依赖的其他 jar 包——规范、完整、准确！一次性解决所有问题！

④ 将项目拆分成多个工程模块

随着 JavaEE 项目的规模越来越庞大，开发团队的规模也与日俱增。一个项目上千人的团队持续开发很多年对于 JavaEE 项目来说再正常不过。那么我们想象一下：几百上千的人开发的项目是同一个 Web 工程。那么架构师、项目经理该如何划分项目的模块、如何分工呢？这么大的项目已经不可能通过 package 结构来划分模块，必须将项目拆分成多个工程协同开发。多个模块工程中有的是 Java 工程，有的是 Web 工程。

那么工程拆分后又如何进行互相调用和访问呢？这就需要用到 Maven 的依赖管理机制。大家请看
我们的 Survey 调查项目拆分的情况：

```flow
st=>start: Web模块：
表述层
componet=>operation: 组件模块：
控制层
业务逻辑层
持久化层
pub=>operation: 公共模块
env=>end: 环境模块

st->componet->pub->env
```

上层模块依赖下层，所以下层模块中定义的 API 都可以为上层所调用和访问。

### 3. mavn what

**1\. 什么是 Maven 简介**

Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理。Maven 这个单词的本意是：专家，内行。

> 其它的构建工具及发展：Make &rarr; Ant &rarr; Maven &rarr; Grable

**2\. 什么是构建**

构建并不是创建，创建一个工程并不等于构建一个项目。要了解构建的含义我们应该由浅入深的从 以下三个层面来看：

① 纯  Java 代码 

大家都知道，Java 是一门编译型语言，.java 扩展名的源文件需要编译成 .class 扩展名的字节码 文件才能够执行。所以编写任何 Java 代码想要执行的话就必须经过编译得到对应的 .class 文件。

②Web 工程 

当我们需要通过浏览器访问 Java 程序时就必须将包含 Java 程序的 Web 工程编译的结果“拿”到服务器上的指定目录下，并启动服务器才行。这个“拿”的过程我们叫部署。

③实际项目 

在实际项目中整合第三方框架，Web 工程中除了 Java 程序和 JSP 页面、图片等静态资源之外，还 包括第三方框架的 jar 包以及各种各样的配置文件。所有这些资源都必须按照正确的目录结构部署到服务器上，项目才可以运行。 所以综上所述：构建就是以我们编写的 Java 代码、框架配置文件、信息等其他资源文件、JSP 页 面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。

3\. 为什么要有 maven (maven 解决了什么问题)?

- 一个项目就是个工程，但如果项目非常庞大，就不适合继续用 package 来划分模块。最好是每一个模块对应一个项目工程，有利于分工协作，借助于 Maven 就可以将一个项目划分多个工程

- 项目中需要的 jar 包需要手动 “粘贴”，“复制” 到 WEB-INF/lib 目录下，同样的 jar 包重复出现在不同的工程中，一方面浪费存储空间，另一方面也让程序变的臃肿。

  借助 Maven，可以将 jar 包仅仅保存在 仓库中，有需要使用的工程，引用这个文件的接口，并不需要把 jar 包复制过来

- jar 包需要别人替我们准备好，或者到官网下载，不同技术的官网提供 jar 包下载的形式五花八门，某些技术的官网就是借助于 maven 或 svn 等工具进行下载，如果以不规范的方式下载 jar 包，那么 jar 包中的内容也可能是不规范的 (存在被他人修改的可能性)。借助于 maven 可以以一种规范的方式下载 jar 包，

- 一个 jar 包依赖其它 jar 包需要自己手动加入到项目中，如果所有 jar 包之间的依赖关系都需要程序员自己非常清楚的了解，那么就会极大的增加学习成本，Maven 会自动将依赖的 jar 包导入进来

2\. 什么是构建

- 概念：以  "Java 源程序"、“框架配置文件”、“JSP”、“HTML”、“图片” 等资源为原材料，去产生一个可以运行的项目的过程。

- 构建的三个过程：编译、部署、搭建

  - 编译：把 Java 源程序编译 .class 字节码文件

  - 部署：一个 BS 项目最终运行的并不是动态 Web 工程本身，而是这个动态 Web 工程编译的结果。

    动态 Web 工程 &rarr; 编译、部署 &rarr; 编译结果
    
    > 开发过程中，所有的路径和配置文件中配置的类路径都是以编译结果的目录结果为准的。

**3\. 构建过程的中的几个主要环节**

- 清理： 将以前编译的旧 class字节码文件删除，为下一次编译做准备
- 编译： 将 Java 源程序编译成 class 字节码文件
- 测试： 针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。
- 报告： 在每一次测试后以标准的格式记录和展示测试结果。测试程序的执行结果
- 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。动态 web 工程打包成 war 包，Java 工程打成 jar 包
- 安装：Maven 特定的概念——将打包的文件复制到仓库中指定的位置
- 部署：将打包的结果部署到 servlet 指定目录下，运行。

**4\. 自动化构建**

其实上述环节我们在 Eclipse 中都可以找到对应的操作，只是不太标准。

Maven 可以自动的从构建过程的起点一直执行到终点

### 4. Maven 的核心概念

Maven 能够实现自动化构建是和它的内部原理分不开的，这里我们从 Maven 的九个核心概念入手， 看看 Maven 是如何实现自动化构建的

- 约定的目录结构
- POM
- 坐标
- 依赖管理
- 仓库
- 声明周期/插件/目标
- 继承
- 聚合

> **Maven 的核心程序中仅仅定义了抽象的生命周期，而具体的操作则是由 Maven 的插件来完成的。**可是 Maven 的插件并不包含在 Maven 的核心程序中，在首次使用时需要联网下载。 下载得到的插件会被保存到本地仓库中。本地仓库默认的位置是：~\\.m2\repository。 如果不能联网可以使用我们提供的 RepMaven.zip 解压得到。具体操作参见“Maven 操作指南.tx

1\. 约定的目录结构

​		约定的目录结构对于 Maven 实现自动化构建而言是必不可少的一环，就拿自动编译来说，Maven 必须能找到 Java 源文件，下一步才能编译，而编译之后也必须有一个准确的位置保存编译得到的字节码文件。

​		我们在开发中如果需要让第三方工具或框架知道我们自己创建的资源在哪，那么基本上就是两种方式：

① 通过配置的形式明确告诉它 

② 基于第三方工具或框架的约定

Maven 对工程目录结构的要求就属于后面的一种。

-  根目录：工程名
   -  源码目录：src
      -  主程序目录：main
         -  主程序 Java 源文件目录：java
         -  主程序的资源文件目录：resources
      -  测试程序目录：test
         -  测试程序的 Java 源文件目录：java
         -  测试程序的资源文件目录：resourcess
   -  编译结果目录：target


- pom.xml 文件：Maven 工程的核心配置文件

  ```
  		Hello
  		|---src
  		|---|---main
  		|---|---|---java
  		|---|---|---resources
  		|---|---test
  		|---|---|---java
  		|---|---|---resources
  		|---pom.xml
  ```

  


现在 JavaEE 开发领域普遍认同一个观点：约定>配置>编码。意思就是能用配置解决的问题就不编码， 能基于约定的就不进行配置。而 Maven 正是因为指定了特定文件保存的目录才能够对我们的 Java 工程进行自动化构建。

### 4. 常用 Maven 命令

- mvn clean：清理
- mvn compile： 编译主程序
- mvn test-compile： 编译测试程序
- mvn test： 执行测试
- mvn package： 打包
- mvn install： 安装
- mvn site：生成站点

注意：执行与构建过程中相关的 Maven 命令，必须进入 pom.xml 所在的目录

### 5.  关于联网问题

① Maven 核心程序中仅仅定义了抽象的生命周期，但是具体的工作必须由特定的插件来完成。而插件本身并不包含在 Maven 核心程序中，在首次使用时需要联网下载。 下载得到的插件会被保存到本地仓库中。

② 当我们执行的 mvn 命令需要用到某些插件时， Maven 核心程序会首先到本地仓库中查找

③ 本地仓库的默认位置：系统中当前用户的家目录\\.m2\repository

例如：`C:\Users\XJ\.m2\repository`

④ Maven 核心程序如果在本地仓库中找不到需要的插件，就会自动连接外网，到中央仓库中中下载

⑤ 如果无法连接外网，则构建失败。

⑥ 修改默认本地仓库的位置，可以让 Maven 核心程序到我们指定的目录下查找插件

1. 找到 Maven 核心程序的解压目录下的 \conf\setttings.xml

2. 在 settings.xml 文件中找到 localRepository 标签

3. 将 `<localRepository>/path/to/local/repo</localRepository>` 从注释中取出

4. 将标签内容修改为已经准备好的 Maven 仓库目录

   例如：`<localRepository>D:/RepMaven</localRepository>`

### 6. POM

① 含义：Project Object Model 项目对象模型。将 Java 工程的相关信息封装为对象，作为便于操作和管理的模型。 Maven 工程的核心配置。

② pom.xml 是 Maven 工程最核心配置文件，与构建过程相关的一些设置都在这个文件中进行设置，重要程度相当于 web.xml 对于 Web 工程。*可以说学习 Maven 就是学习 pom.xml 文件中的配置。*

### 7. 坐标

> 数学中的坐标
>
> 1. 在平面上，使用 X、Y 连个向量可以定位平面上的任何中的任意一点
> 2. 在空间上，使用 X、Y 、Z 三个向量可以定位空间中的任何一个点。

Maven 的坐标

使用以下三个向量在 Maven 仓库中定位一个 Maven 工程

- groupid：公司或组织域名倒写 + 当前项目名称 例如：`<groupId>com.apache.maven</groupId>`

- artifactid 当前项目模块名称  例如：`<artifactId>Hello<artifactId>`

- version 当前模块的版本

  `<version>0.0.1-SNAPSHOT<Version>`

> realease 长期版本
>
> snap-short 短期版本

如何通过坐标在项目中查找？

```xml
<groupId>org.springframework</groupId>
<artfactId>spring-core</artfactId>
<version>4.0.0.Realease<version>
```

① 将 gav 三个向量连接起来

`org + springframework + spring-core + 4.0.0.Realease.jar`

② 以连接起来的字符串作为目录结构到仓库中查找

`org/springframework/spring-core/4.0.0.Realease/spring-core-4.0.0.Realease.jar`

### 8. 仓库

① 仓库的分类：

- 本地仓库：当前电脑上部署的仓库目录。为当前电脑上所有 Maven 工程服务
- 远程仓库：
  - 私服：搭建在局域网环境中，为当前局域网范围内的所有 Maven 工程服务
  - 中央仓库：假设在 Internet 上，为全世界所有 Maven 工程服务
  - 中央仓库的镜像：架设在各个大洲，为中央仓库的分担流量。减轻中央仓库的压力，同时更快的响应用户请求

仓库中保存的内容：Maven 工程

1. Maven 自身所需要的插件
2. 第三框架或工具的 jar 包
3. 我们自己开发的 Maven 工程(项目的模块)

### 9. 依赖

Maven 中关键的部分，我们使用 Maven 最主要的就是使用它的依赖管理功能。要理解和掌握 Maven 的依赖管理，我们只需要解决一下几个问题：

①依赖的目的是什么

当 A jar 包用到了 B jar 包中的某些类时，A 就对 B 产生了依赖，这是概念上的描述。那么如何在项目中以依赖的方式引入一个我们需要的 jar 包呢？

答案非常简单，就是使用 dependency 标签指定被依赖 jar 包的坐标就可以了。例如：

```java
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>Hello</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <scope>compile</scope>
</dependency>
```

> Maven 解析依赖信息时会到本地仓库中查找依赖的 jar 包
>
> 对于我们自己开发的 Maven 工程，使用 mvn install 命令安装后就可以进入仓库

② 依赖的范围

大家注意到上面的依赖信息中除了目标 jar 包的坐标还有一个 scope 设置，这是依赖的范围。依赖的范围有几个可选值，我们用得到的是：compile、test、provided 三个。

|         | 对主程序是否有效 | 对测试程序是否有效 | 是否参与打包 | 是否参与部署 | 典型例子        |
| ------- | ---------------- | ------------------ | ------------ | ------------ | --------------- |
| compile | 有效             | 有效               | 参与         | 参与         | spring-core     |
| test    | 无效             | 有效               | 不参与       | 不参与       | junit           |
| provide | 有效             | 有效               | 不参与       | 不参与       | servlet-api.jar |

③ 依赖的传递性

A 依赖 B，B 依赖 C，A 能否使用 C 呢？那要看 B 依赖 C 的范围是不是 compile，如果是则可用，否则不 可用。（模块 B、C 的父工程 A，那么 B 依赖了某个jar 包，compile 范围，那么 B，A 都会依赖这个 jar 包，这也是依赖的传递性）

<table border="1px" cellspacing="0px" align="center">
    <tr>
        <th colspan="3">Maven 工程</th>
        <th>依赖范围</th>
        <th>对 A 可见性</th>
    </tr>
    <tr>
        <td rowspan="3">A</td>
        <td rowspan="3">B</td>
        <td>C</td>
        <td>compile</td>
        <td>√</td>
    </tr>
    <tr>
        <td>D</td>
        <td>test</td>
        <td>x</td>
    </tr>
    <tr>
        <td>E</td>
        <td>provided</td>
        <td>×</td>
    </tr>
</table>

④ 依赖的排除

如果我们在当前工程中引入了一个依赖是 A，而 A 又依赖了 B，那么 Maven 会自动将 A 依赖的 B 引入当 前工程，但是个别情况下 B 有可能是一个不稳定版，或对当前工程有不良影响。这时我们可以在引入 A 的时候将 B 排除

```xml
/*
在 HelloFriend 项目中对 commons-logging：1.1.1[compile] 排除
*/
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>HelloFriend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <type>jar</type>
    <scope>compile</scope>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

⑤ 统一管理所依赖 jar 包的版本

对同一个框架的一组 jar 包最好使用相同的版本。为了方便升级框架，可以将 jar 包的版本信息统一提 取出来

1\. 统一声明版本号

```xml
<properties>
	 <atguigu.spring.version>4.1.1.RELEASE</atguigu.spring.version>
</properties>
//其中 atguigu.spring.version 部分是自定义标签。
```

2\. 引用前面声明的版本号

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${atguigu.spring.version}</version>
    </dependency>
	……
</dependencies>
```

3\. 其他用法：设置编码

```xml
<properties>
 	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

⑥ 依赖的原则，解决 jar 包冲突

1\. 路径最短者优先

2\. 路径相同时先声明者优先

tips: 这里“声明”的先后顺序指的是 dependency 标签配置的先后顺序。

### 10. 生命周期

① Maven 生命周期定义了各个构建环节的执行顺序，不能打乱，必须按照既定的正确顺序来执行。有了这个清单，Maven 就可以自动化的执行构建命令了。

② Maven 的核心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的

③ Maven 核心程序为了更好的实现自动化构建，按照这一特点执行，按照这一特点执行生命的周期的各个阶段：不论现在要执行生命周期的哪一个阶段，都是从生命周期最开始的命令开始执行。

| 生命周期阶段 | 插件目标    |
| ------------ | ----------- |
| compile      | compile     |
| test-compile | testCompile |

⑤ Maven 有三套相互独立的生命周期，分别是： 

1. Clean Lifecycle 在进行真正的构建之前进行一些清理工作。

2. Default Lifecycle 构建的核心部分，编译，测试，打包，安装，部署等等。 

3. Site Lifecycle 生成项目报告，站点，发布站点。

它们是相互独立的，你可以仅仅调用 clean 来清理工作目录，仅仅调用 site 来生成站点。当然你也可以 直接运行 mvn clean install site 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。

**clean 生命周期**

Clean 生命周期一共包含了三个阶段： 

​	① pre-clean 执行一些需要在 clean 之前完成的工作

​	② clean 移除所有上一次构建生成的文件 

​	③ post-clean 执行一些需要在 clean 之后立刻完成的工作

**Site 生命周期**

① pre-site 执行一些需要在生成站点文档之前完成的工作

② site 生成项目的站点文档 

③ post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备 

④ site-deploy 将生成的站点文档部署到特定的服务器上 

这里经常用到的是 site 阶段和 site-deploy 阶段，用以生成和发布 Maven 站点，这可是 Maven 相当强大的功能，Manager 比较喜欢，文档及统计数据自动生成，很好看。

**Default 生命周期**

Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里， 只解释一些比较重要和常用的阶段： 

validate 

generate-sources 

process-sources

generate-resources process-resources 复制并处理资源文件，至目标目录，准备打包。 

compile 编译项目的源代码。 

process-classes 

generate-test-sources 

process-test-sources 

generate-test-resources 

process-test-resources 复制并处理资源文件，至目标测试目录。 

test-compile 编译测试源代码。 

process-test-classes 

test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。 

prepare-package 

package 接受编译好的代码，打包成可发布的格式，如 JAR。 

pre-integration-test 

integration-test 

post-integration-test 

verify 

install 将包安装至本地仓库，以让其它项目依赖。 

deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

**生命周期与自动化构建**

运行任何一个阶段的时候，它前面的所有阶段都会被运行，例如我们运行 mvn install 的时候，代码会 被编译，测试，打包。这就是 Maven 为什么能够自动执行构建过程的各个环节的原因。此外，Maven 的插 件机制是完全依赖 Maven 的生命周期的，因此理解生命周期至关重要。

### 11. 插件和目标

① Maven 的核心仅仅定义了抽象的生命周期，生命周期的各个阶段仅仅定义了要执行的任务是什么，具体的任务都是交由插件完成的。

② 每个插件都能实现多个功能，每个功能就是一个插件目标。各个阶段和插件的目标是对应的。相似的目标使用特定的插件来完成

③ Maven 的生命周期与插件目标相互绑定，以完成某个具体的构建任务

tips: 可以将目标看作为 **调用插件的命令**。例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。

### 11. 在 eclipse 中使用 Maven

① Maven 插件：Eclipse 内置

② Maven 插件的设置：

	- installations： 指定 Maven 核心程序的位置，不建议使用插件自带的 Maven 程序
	- user settings: 指定 conf/settings.xml 的位置，进而获取本地仓库的位置

③ 基本操作：

- 创建 Maven 版的 Java 工程
- 创建 Maven 版的 Web 工程
- 执行 Maven 命令

###  12. 继承

1\. 为什么需要继承机制？

由于非 compile 范围的依赖信息是不能在“依赖链”中传递的，所以有需要的工程只能单独配置。例如：junit

此时如果项目需要将各个模块的 junit 版本统一为 4.9，那么到各个工程中手动修改无疑是非常不可取的。 使用继承机制就可以将这样的依赖信息统一提取到父工程模块中进行统一管理。

2\. 创建父工程

创建父工程和创建一般的 Java 工程操作一致，唯一需要注意的是：打包方式处要设置为 pom。

4\. 在子工程中声明父工程

格式：

```xml
<parent>
<!-- 父工程坐标 -->
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
    <relativePath>从当前目录到父项目的 pom.xml 文件的相对路径</relativePath>
</parent>
```

举例：

```xml
<!-- 子工程中声明父工程 -->
<parent>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>Parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- 以当前文件为基准的父工程pom.xml文件的相对路径 -->
    <relativePath>../Parent/pom.xml</relativePath>
</parent>
```

Tips: 此时如果子工程的 groupId 和 version 如果和父工程重复则可以删除。

5\. 在父工程中管理依赖

① 将 Parent 项目中的 dependencies 标签，用 dependencyManagement 标签括起来

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

② 在子项目中重新制定需要的依赖，删除范围和版本号。

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
</dependencies>
```

### 13. 聚合

1\. 为什么要使用聚合？

将多个工程拆分为模块后，需要手动逐个安装到仓库后依赖才能够生效。修改源码后也需要逐个手动进行 clean 操作。而使用了聚合之后就可以批量进行 Maven 工程的安装、清理工作。

2\. 如何配置聚合？

在总的聚合工程中使用 modules/module 标签组合，指定模块工程的相对路径即可

```xml
<modules>
    <module>../Hello</module>
    <module>../HelloFriend</module>
    <module>../MakeFriends</module>
</modules>
```

### 14\. Maven 

我们可以到 http://mvnrepository.com/搜索需要的 jar 包的依赖信息。

###  依赖【高级】

① 依赖的传递性

在模块中有依赖了某个 jar 包之后，那么它的同级及父级模块都有了该 jar 包的依赖

② 依赖的排除

③ 依赖的原则

④ 统一管理依赖的版本id