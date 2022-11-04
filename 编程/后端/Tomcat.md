# Tomcat

##### 1\. JavaWeb

1\. 什么是 JavaWeb?

JavaWeb 是指，所有通过 Java 语言编写可以通过浏览器访问的程序的总称，叫 JavaWeb。是基于请求和响应来开发的

2\. 什么是请求？

请求是指客户端给服务器发送数据，叫请求 Request。

3\. 什么是响应?

响应是指服务器给客户端回传数据，叫响应 Response。

4\. 请求和响应的关系

请求和响应是成对出现的，有请求就有响应。

##### 2.  Web资源的分类

web 资源按实现的技术和呈现的效果的不同，又分为静态资源和动态资源两种。

- 静态资源： html、css、js、txt、mp4 视频 , jpg 图片……
- 动态资源： jsp 页面、Servlet 程

##### 3. 常用的 web 服务器

① Tomcat：由 Apache 组织提供的一种 Web 服务器，提供对 jsp 和 Servlet 的支持。它是一种轻量级的 javaWeb 容器（服务器），也是当前应用最广的 JavaWeb 服务器（免费）。
② Jboss：是一个遵从 JavaEE 规范的、开放源代码的、纯 Java 的 EJB 服务器，它支持所有的 JavaEE 规范（免费）。
③ GlassFish： 由 Oracle 公司开发的一款 JavaWeb 服务器，是一款强健的商业服务器，达到产品级质量（应用很少）。
④ Resin：是 CAUCHO 公司的产品，是一个非常流行的服务器，对 servlet 和 JSP 提供了良好的支持，性能也比较优良，resin 自身采用 JAVA 语言开发（收费，应用比较多）。
⑤ WebLogic：是 Oracle 公司的产品，是目前应用最广泛的 Web 服务器，支持 JavaEE 规范，而且不断的完善以适应新的开发要求，适合大型项目（收费，用的不多，适合大公司）。

##### 4.T omcat 服务器和 Servlet 版本的对应关系

当前企业常用的版本 7.*、8.*

Servlet 程序 2.5 版本是现在世面使用最多的版本（xml 配置） 到了 Servlet3.0 之后。就是注解版本的 Servlet 使用。

![version](version.png)

##### 5. Tomcat 的使用

1\. 安装: 找到你需要用的 Tomcat 版本对应的 zip 压缩包，解压到需要安装的目录即可

下载地址：<https://tomcat.apache.org/download-10.cgi>

2\. 目录介绍

- bin： 专门用来存放 Tomcat 服务器的可执行程序 
- conf：专门用来存放 Tocmat 服务器的配置文件 
- lib： 专门用来存放 Tomcat 服务器的 jar 包 
- logs： 专门用来存放 Tomcat 服务器运行时输出的日记信息 
- temp： 专门用来存放 Tomcat 运行时产生的临时数据 
- webapps： 专门用来存放部署的 Web 工程。
- work： 是 Tomcat 工作时的目录，用来存放 Tomcat 运行时 jsp 翻译为 Servlet 的源码，和 Session 钝化的目录

3\. 如何启动 Tomcat 服务器？

第一种方式：找到 Tomcat 目录下的 bin 目录下的 startup.bat 文件，双击，就可以启动 Tomcat 服务器。

第二种方式：

① 打开命令行窗口

② 切入到 Tomcat 的 bin 目录下

③ 输入启动命令：`catalina run`

注：在 Linux 系统中是 startup.sh 文件

4\. 测试 Tomcat 服务器启动成功？

打开浏览器，在浏览器地址栏中输入以下地址测试：

① http://localhost:8080

② http://127.0.0.1:8080 

③ http://真实 ip:8080

然后回车如果出现 Tomcat 页面就说明启动成功，否则就没有启动成功。

5\. 可能遇到的问题？

常见的启动失败的情况有，双击 startup.bat 文件，就会出现一个小黑窗口一闪而来。 这个时候，失败的原因基本上都是因为没有配置好 JAVA_HOME 环境

常见的 JAVA_HOME 配置错误有以下几种情况： 

① JAVA_HOME 必须全大写

② JAVA_HOME 中间必须是下划线，不是减号

③ JAVA_HOME 配置的路径只需要配置到 jdk 的安装目录即可。不需要带上

6\. Tomcat 停止运行？

第一种：点击 Tomcat 服务器窗口的 x 关闭按钮

第二种：把 Tomcat 服务器窗口置为当前窗口，然后按快捷键 Ctrl+C

第三种：找到 Tomcat 的 bin 目录下的 shutdown.bat 双击，就可以停止 Tomcat 服务器

6\. 修改 Tomcat 的端口号

- Mysql 默认的端口号：3306
- Tomcat 默认的端口号：8080

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

Tips: HTTP 协议默认的端口号是：80

##### 6. 部署 web 工程到 Tomcat ?

1\. 部署方法

第一种：只需要把 web 工程拷贝到 Tomcat 的 webapps 目录下即可。

第二种：找到 Tomcat 下的 conf\Catalina\localhost\ 下,创建如下的配置文件：

abc.xml 配置文件内容如下：

```xml
<!-- Context 表示一个工程上下文
path 表示工程的访问路径:/abc
docBase 表示你的工程目录在哪里
-->
<Context path="/abc" docBase="E:\book" />
<!--注意文件名必须和 path 值一致，例如这里的文件名 abc,path 值为 /abc-->
<!--访问这个工程的路径如下:http://ip:port/abc/ 就表示访问 E:\book 目录-->
```

2\. 如何访问 webapps 下的 web 工程？

只需要在浏览器中输入访问地址格式如下： http://ip:port/工程名/目录下/文件名

##### 7\. 手拖动 html 页面到浏览器和在浏览器中输入 `http://ip:端口号/工程名/` 访问的区别？

①  手拖动 html 页面到浏览器的原理：`file://文件路径`

协议是 file,表示告诉浏览器直接读取 file: 协议后面的路径，解析展示在浏览器上即可。(浏览器直接本地文件进行解析展现)

② 输入访问地址的原理：`http://ip:port/工程名/资源名`

使用的协议是 http 协议。

##### 8\. ROOT 的工程的访问，![http原理](http%E5%8E%9F%E7%90%86.png)以及 默认 index.html 页面的访 问

##### 9. Root 的工程的访问，以及 默认 index.html 页面的访 问

当我们在浏览器地址栏中输入访问地址如下：

```http
//没有工程名的时候，默认访问的是 ROOT 工程。
http://ip:port/ 
//没有资源名，默认访问 index.html 页面
http://ip:port/工程名 
```

10\. IDEA 中使用 Tomcat

详见 尚硅谷_Tomcat_王振国%20-%20课堂笔记.pdf