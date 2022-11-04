# HTTPS

### 第一章 HTTP 介绍

##### 1.1 什么是 Web

**1.1.1 理解 Web**

中文名是万维网，也被称为 WWW（World Wide Web 环球信息网）。

Web 最核心的组成部分是 HTTP，HTTP 由服务器和客户端组成，有了 HTTP，互联网上的不同终端才能够交换信息。

- Web 是一种信息索取方式，是互联网的一部分。

- Web 是基于超文本相互关联而构成的 (逻辑) 系统

HTTP 的作用是什么？

在 Web 中，用户是信息索取方，浏览 Web 信息的软件叫作客户端， 服务器是信息的提供方，负责信息的检索和发送。为了请求和响应数据，客户端和服务器通过 HTTP 协议来完成一系列数据的交换。HTTP 并不负责数据传输，仅负责请求和响应，真正的数据传输由其他网络层处理。

> 协议：通信双方必须要遵守的规则和约定。
>
> 互联网中的终端（节点）？
>
> 世界上任何设备只要支持 TCP/IP ,就可以称为互联网上的一个终端。通信是手段，通信的目的是信息共享。

**1.1.2 Web 的组成**

TCP/IP 可以使信息从一端发送到另一端，但是用户并不明白字节流（看不懂字由 01 组成的序列），计算机需要对其进行翻译，需要指定一个标准，双方基于同样的标准才能理解数据的含义。

互联网中的数据类型：在 HTTP 中通过 Content-Type 头信息表示数据传输类型

19080 蒂姆.伯纳斯.李 Tim.Berners-Lee 教授提出设想，Web 主要包括三个技术： URL、HTML、HTTP

- HTTP（HyperText Transfer Protocol，超文本传输协议）
- URL 表示互联网上某个资源的地址
- HTML 超文本标记语言

##### 1.2 理解 HTTP

HTTP 目前的版本是 HTTP 2.0（RFC2616）

**HTTP 模型 B/S，由浏览器和服务器组成**

> C = Client, S = Server。C/S 架构即“客户端-服务器” 架构。这里的“客户端”可以是有 GUI （图形用户界面）的定制软件，也可以是浏览器，甚至可以是通过 SSH 访问服务器的命令行脚本。只要是客户端通过访问服务器调取计算或者存储资源的，统统都是 C/S 架构。所谓的 Browser-Server 架构其实是 C/S 架构的一种特殊的实现形式，而不是其对立面

注意：C/S 和 B/S 并不是对立的，B/S 是 C/S 的子集。

**HTTP 语义**

HTTP session 主要包括两部分：分别是 HTTP 语义和 HTML 实体*。*

**1.** HTTP header

访问某个 URL，浏览器根据用户的行为和终端环境构建消息结构体。

> HTTP 报文分 2 个： 一个是 HTTP 请求报文，一个是 HTTP 响应报文。
>
> HTTP 请求报文分为 3 部分：第一部分 起始行（ Request line ），第二部分 请求首部 (Request Header ），第三部分 请求主体（ Body ）。 第一行中的 ethod 表示请求方法， 如“POST ”或者“ GET ”等。
>
> 响应报文也分为三部分：第一部分叫 响应行（Response Line），第二部分叫响应首部 (Response body)，第三部分 响应体(Reponset body)

**3.** 响应行由 HTTTP 版本、状态码、信息提示符组成。

例如：`HTTP/1.1 200 OK`。 HTTP/1.1 表示本次响应 支持 HTTP/1.1；200 表示本次请求被正确处理了，如果是 404 表示服务器上不存在客户端 需要的资源；信息提示符和状态码是一一对应的，不同的状态码有不同的描述信息。

1.3 HTTP 特点

- 客户端/服务器模型 C/S 

  	HTTP 本身是不能传输的，需要通过网络层中其它协议进行通信，一般是构建 TCP 之上。TCP 提供一个可靠的，面型连接传输服务。**客户端和服务器是否正确传输依 赖于 TCP 这个协议。**

- 无状态的

  HTTP 是基于 TCP 的，当一个 TCP 连接关闭后，所有的 HTTP 请求/响应信息将全部 消失。

  所谓的无状态就是每次请求完成 后，不会在客户端和服务器上保存任何的信息，对于客户端和服务器来说，根本不知道上 一次请求的信息是什么，甚至不知道本次连接的对端是不是上次连接的那一端，它的生命周期随着 TCP/IP 连接的关闭结束了

  现在又出现了 cookie 和 session 技术可以保持状态码。但 cookie 技术设计得非常不严谨，引发了很多安全问题。

	> 客户端通过 Socket 技术创建 TCP/IP 连接。
	>
	> Cookie 是如何进行会话保持的，客户端第一次请求完成后，服务器会发送 Cookie 信息到客户端的计算机上，客户端下次请求的时候会携带该 Cookie 信息，这样服 务器就知道该用户就是上一次请求的用户了。
	
- HTTP 是跨平台的

  HTTP 就是具备一定规则的纯文本信息，任何开发语言都 可以实现 HTTP 或者基于 HTTP 进行开发，开发出来的软件也很容易移植，受系统环境的 影响非常少。

- HTTP 用途广泛

  Web 主要使用 HTTP 进行传输数据，HTTP 更多的是一个数据载体，对于 Web 应用来 说更重要的是浏览器如何处理这些数据

##### 1.3 网络模型

HTTP 是应用层协议，应用层协议是 TCP/IP 的一部分。

**1.** TCP/IP 概述

OSI 模型是一个通用的网络协议模型，实际应用的确实 TCP/IP 模型。TCP/IP 包含的不仅仅是 TCP 或者 IP，它是一个协议族。

<img src="">

> 任何协议都是一种标准，标准就是通信双方需要遵循的共同的规则

**2.**TCP/IP 特点：

- 分层
- 拆包\封包机制

TCP/IP 对网络进行了抽象并划分了四层：应用、传输、网络、链路；每一层的特点不同，完成各自的任务。好处：清晰的描述了每一层的职责，当网络应用程序出现问题后，能够迅速定位到那一层出现了问题并解决。

每一层的工作：

- 应用层；我们传输的都是字节流的信息，如果没有应用层，那么传输的内容就仅仅是数据而已，人类无法理解。但是应用层能够结束数据的含义。例如 HTTP 中的 Content-type，就指明了它是一个什么类型的数据。开发者开发的软件一般都是应用层软件

- 传输层：传输层收到应用层消息后，负责连接到服务器

  传输层通过端口号来区分服务，IP 地址 + 端口号才能构建一条传输通道。（服务器默认端口号：80）

  传输层主要又 TCP/UDP。，TCP 能够保证数据正确地到达，一旦出现错误，会有一 系列处理机制，比如重发和校验机制，保证数据正确地传输到对端。UDP 不能保证数据正确传达

  **TCP 三次握手**

- 网络层：主要是 IP 协议。户端和服务器传输的时候，会经过很多节点，IP 就是 选择一条最优的路径。

- 链路层

  只有链路层是实体的，包括光纤、网卡等设备

  对于客户端请求来说，传输层接收到应用层消息后， 在 HTTP 数据包前面增加 TCP 包头，然后发送给网络层；网络层在 TCP 数据包前面加上 IP 包头发送给链路层；链路层在 IP 数据包前面加上以太网包头；最终服务器接收到完整 的数据包。 然后服务器进行拆包：首先在网络层去除链路层包头；在传输层去除 IP 包头；在应 用层去除 TCP 包头；最终得到完整的 HTTP 应用层数据

**3.** Socket 和 TCP

##### 1.4 协议安全分析

**代理服务器**


##### URL 和 URI

URL(Uniform Resource Locator) 统一资源定位符，是统一资源标识符 URI (Uniform Resource Identifier)的子集.URL 描述了请求资源在某个服务器的位置信息。

##### 报文

1\. 什么是报文

报文是按一定格式组织起来的数据。分为请求报文和响应报文。

- 请求报文包括: 请求方法、版本协议以及请求头信息。
- 响应报文包括: 版本协议、状态响应码、响应头信息、响应内容

##### 请求方法

在客户端向服务器发送请求，需要确定请求方法。请求方法表明了 URL 对指定资源的操作方式。服务器会根据不同的请求方法进行不同的操作。

- GET: 发送请求获取服务器上的特定资源。
- POST: 向服务器提交数据，请求服务器进行处理。通常，表单提交需要用到 POST
- HEAD: 只获取资源头信息
- PUT: 使用客户端向服务器发送的数据替代指定的内容
- DELETE: 请求服务器删除指定资源
- CONNECT: 在客户端配置代理的情况下，使用 CONNECT 建立客户端与服务器之间的联系
- OPTIONS: 访问服务器支持的请求方法，允许客户端查看服务器的性能
- TRACE: 对可能经过代理服务器传送到服务器的报文进行跟踪

##### 5. HTTP 状态码

描述了客户端向服务器请求过程中发生的状况，由三位数字组成

<img src="data:image/jpeg;base64,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":>

| 数字 |       类型       |                             描述                             |
| :--: | :--------------: | :----------------------------------------------------------: |
| 1xx  |   信息性状态码   |            服务器收到请求，需要请求者继续执行操作            |
| 2xx  |  响应成功状态码  |          客户端的请求成功并被服务器处理，返回响内容          |
| 3xx  |   重定向状态码   | 客户端请求的 URL 被转移到新的 URL，需要进行附加操作以完成请求 |
| 4xx  | 客户端错误状态码 |               客户端请求的语法错误或网页不存在               |
| 5xx  |  服务器端状态码  |                  服务器在处理请求时发生错误                  |

以下为一些常见的状态码

1\. 状态码 2xx

- 200 客户端发送的请求已被服务器正常处理
- 204 表示客户端发送的请求已被服务器正常处理，但返回的响应报文不包含实体的主体部分（也就是没有获得数据主体）

2\. 状态码 3xx

- 301 永久性重定向，表示请求的资源已被分配到新的 URL
- 302 临时性重定向，表示请求的资源已被分配到新的 URL，希望用户使用新的 URL访问。如用户登录某网站后会通过 302 跳转到登录后的主页。
- 303 临时重定向，告知客户端请求的资源存在另一个 URL，使用 GET 方法定向获取请求资源

3\. 状态码3xx

- 403 表示客户端的请求被服务器拒绝，一般不说明拒绝原因。（使用网络爬虫短时间内获取大量数据时，会被服务器认定为攻击行为，服务器可能会产生拒绝行为，同时本机的 IP 也会被封禁。）

- 404 表示服务器无法找到 URL 对应的资源。通常服务器会返回一个实体，以便客户端展示给用户。

4\. 状态码 5xx

- 500 表示服务器在执行请求时发生了错误，也有可能是 Web 应用存在 Bug 或临时性故障
- 503 表示服务器暂时处于超负载或正在进行停机维护状态，无法处理请求。

##### 6. HTTP 信息头

HTTP 信息头，也称为头字段或首部，具有传递额外重要信息的作用。HTTP 信息头包括 4 类：通用头（General Header）、请求头 （Request Header）、响应头（Reponse Header）和实体头（Entity Header）。其中请求头和响应头只在请求信息和响应信息中出现，而通用头和实体头在请求信息和响应信息中都可出现。只有消息中包含实体数据时，实体头才会出现。

HTTP 信息头是由字段名和字段值组成。

###### 1\. 通用头

HTTP 通用头的字段名及其功能

| 字 段 名          | 功能                                                         |
| ----------------- | ------------------------------------------------------------ |
| Cache-Control     | 请求和响应遵循的缓存机制                                     |
| Connection        | 客户端和服务器指定与请求或响应连接有关的选项，例如是否需要持久连接 |
| Date              | 创建 HTTP 报文的时间，即信息发送时间                         |
| Pragma            | 包含用来实现特定的指令，通常使用 Pragma:no-cache             |
| Trailer           | 表明以 chunked 编码传输的报文实体数据尾部存在的字段          |
| Transfer-Encoding | 规定了传输报文实体数据采用的编码方法                         |
| Upgrade           | 检测 HTTP 协议，允许服务器指定一种新的协议                   |
| Via               | 追踪客户端与服务器之间的请求报文和响应报文的传输路径（网管、代理服务器等） |
| Warning           | 告知用户与缓存相关的警告                                     |

**Cache-Control** 

Cache-Control 指令按请求和响应分类。请求消息中的缓存指令包括 no-cache、no-store、max-age、max-stale、min-fresh 和 only-if-cached 等，响应消息中指令包括 public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate 和 max-age 等。

Chache-Control 请求指令及说明

| 指令          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| no-cache      | 告知服务器不直接使用缓存,目的是防止从缓存中返回过期的资源    |
| no-store      | 提示请求或响应中包含机密信息，规定不缓存请求或响应中的任何内容 |
| max-age       | 客户端希望接受存在时间不超过规定秒数的资源                   |
| max-stale     | 客户端希望结识存在时间超过规定秒数的资源                     |
| min-fresh     | 客户端希望接受还未超过指定秒数的缓冲资源                     |
| only-if-cache | 客户端仅在服务器本地已缓存目标资源的情况下，才要求服务器返回资源 |

Chache-Control 响应指令及说明

| 指令             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| public           | 可向任意方提供响应缓存                                       |
| private          | 仅向特定用户提供相应缓存                                     |
| no-cache         | 不能直接使用缓存，要向服务器发送验证                         |
| no-store         | 提示请求或相应中包含机密信息，规定不缓存请求或相应中的任何内容 |
| no-transform     | 不得对资源进行转换或转变                                     |
| max-age          | 告知客户端，资源在规定的秒数内是最新的，无需向服务器发送新请求 |
| must-revalidate  | 可以缓存但必须由服务器发出验证请求，请求失败返回 504         |
| proxy-revalidate | 要求中间缓存服务器（如代理）对缓存的相应有效性在进行确认     |

**Connection**

主要指令由 Upgrade、keep-alive、close。

- Upgrade 用于检测协议是否可以使用更高版本连接控制不在转发给代理头字段。

  > HTTP 1.1 版本默认使用持久连接，当服务器想断开连接时，则 Connection 指定为 close。
  >
  > HTTP 1.1 之前的版本默认使用非持久连接，当客户端需要保持网络连接打开时，则 Connection 需要只当为 keep-alive

**Date** ： 创建 HTTP 报文的时间

**Progma**: HTTP/1.1 版本之前的通用头。仅作为向后兼容支持 HTTP/1.0 协议中的缓存服务器。(也就是兼容之前的版本协议)。规范定义的形式唯一。格式: `Pragma:no-cache` 效果与 HTTP/1.1 中的 `Cache-Control:no-cache` 相同

**Trailer**: 允许服务器在发送请求报文主体后面添加额外的内容。Tailer 后面的字段值为报文主体后面索要追加的字段名称

```http
HTTP/1.1 200 OK
…
Trailer:expires
//HTML 内容
0//通过来进行分割
Expires: Tue,09  Oct 2019 00:09:18 GMT
```

tips: 注意这里写的报文主体后面，不是实体主体

**Transfer-Encoding**: 主要指令有：chunked、compress、deflate、gzip、identity 每种指令代表一种数据压缩算法。可以使用多个值

**Upgrade**: 用于向服务器指定某种传输协议以便服务器进行转换，使用 Upgrade 时需要额外指定 `Connection:Upgrade`

例如：`Upgrade:HTTP/2.0,SHTTP/1.3,IRC/6.9,RTA/XLL`

**Via**: 由代理服务器添加，适用于正向或反向代理，在请求和相应中均可出现。用法需要规定所使用的协议版本号、公共代理的 URL及端口号、内部代理的名称或别名等。

例如：`Via:1.1 7fdd…6.*****.net(********)`

Warning: 使用 warning 时需要规定所使用的三位数字警告码、添加到 Warning 首部的服务器、软件名称或伪名称（当使用代理未知时可以使用 - 代替）、描述警告信息的文本和日期时间（可选）

例如：`Warning:112 - "cache down" Tue, 09 Oct 2018 00:09:08 GMT`

###### 2\. **请求头**

1\. 什么是请求头？

请求头是客户端向服务器发送请求报文时所用字段。服务器根据请求头为客户端提供相应。在进行网络爬虫时，为了更好的模拟浏览器访问浏览器通常需要设置一些请求头信息。

*部分 HTTP 请求头及其功能*

| 字段名                    | 功能（描述）                                                 |
| ------------------------- | ------------------------------------------------------------ |
| Accept                    | 指定客户端可以处理的数据类型                                 |
| Accept-Charset            | 指定客户端可以接受的字符集                                   |
| Accept-Encoding           | 指定浏览器能够进行解码的数据编码格式                         |
| Accept-Language           | 指定浏览器可以接受的数据种类                                 |
| Cookie                    | 客户端发送请求时，会把保存在域名下的所有 Cookie 值一起发送给服务器 |
| Host                      | 指定请求服务器的域名和端口号，不包括协议                     |
| Origin                    | 指定请求的服务器名称，即包括协议和域名                       |
| Referer                   | 告知服务器请求的原始资源的 URL,即包括协议、域名、端口等信息  |
| Upgrade-Insecure-Requests | 向服务器发送一个信号，表示客户端对加密和认证响应的偏好       |
| User-Agent                | 发起请求的应用名称（也就是浏览器标识码）                     |

**Accept**: 使用形式 type/subtype,使用时一般一次指定多种数据类型。

tips: 指定的多个数据类型之间用逗号隔开，q 表示给数据类型设置优先级，取值范围 (0,1] ，如果不设置默认为 1

例如：`Accept: text/css,*.*;q=0.1`

**Accept-Charset**：*一般一次指定多种字符集*

**Accept-Encoding**: 常用的编码方式有 gzip、deflate、br、sdch、compress 和 identity

**Accept-Language**: 

- zh-CN 简体中文
- zh 中文
- en-US 英语-美股
- en 英语

Tips: 使用时可以设置语言的优先级 `Accept-Language:zh-CN,zh;q=0.9,en;q=0.8`

**Cookie**: 语法：`Cookie:name=value`,包含多个 name 和 value 值；中间用 `;` 隔开

**Host**: 所有的 HTTP 请求必须携带 Host 头。例如: `Host:www.caoniang.com`

**Origin**: 一般用于 CORS（Cross-origin resource sharing） 跨域请求和 POST 请求中

**Referer**: 浏览器向服务器发送请求主要有两种方式，即用户在浏览器中直接输入 URL；用户通过单机网页上的超链接访问。通常情况下使用第一种方式浏览器不会发送 Refer 信息给服务器，而使用第二种方式则会发送信息给服务器。因此 Referer 常被网站管理人员用来网站的访问者是如何导航进入网站的。

在网络爬虫中，经常需要在请求的 heads 中伪造 Referer 来绕过一些防爬措施。

**Upgrade-Insecure-Requests**: 用于升级不安全请求，指示浏览器在进行 URL 请求之前，升级不安全的网站。例如：`http://www.***.com`升级成`https://www***.com`

**User-Agent**: 用于标识请求浏览器的身份。

> 很多网站为了防止自身数据被网络爬虫采集，都会对请求头进行校验，其中最重要的就是 User-Agent。而网络爬虫为了更好的采集数据，通常需要使用 Uset-Agent 库，每次访问 URL 都会从 User-Agent 库中随机挑选一个使用，进而达到伪造浏览器安全访问服务器资源的目的。

###### 3\. 响应头

1\. 什么是响应头？

响应头是服务器向客户端发送响应报文所使用的字段。

HTTP 响应头的字段及功能

| 字段名        | 功能                                                         |
| ------------- | ------------------------------------------------------------ |
| Accept-Ranges | 指定服务器对资源请求的可接受范围类型，字段的值定理范围类型的单位 |
| Age           | 服务器产生相应经过的时间，单位为秒，为非负整数，主要用于缓存 |
| Set-Cookie    | 用来服务器向客户端发送 Cookie                                |
| Server        | 指明服务器软件及版本号                                       |
| Vary          | 告知代理是使用缓存相应还是从源服务器中重新获取资源           |

###### 4\. 实体头

请求报文和响应报文中经常包含一些实体数据（提交表单、服务器给浏览器返回的网页等数据）。实体头提供了大量有关实体数据的信息。包括实体数据的类型、长度、和压缩方法等。

**HTTP 实体头的字段名及其功能**

| 字段名           | 功能                                     |
| ---------------- | ---------------------------------------- |
| Allow            | 列出资源所支持的 HTTP 方法集合           |
| Content-Encoding | 告知客户端服务器对实体数据的编码方式     |
| Conent-Lanauage  | 告知客户端实体数据使用语言类型           |
| Content-Length   | 实体数据的长度                           |
| Content-Location | 实体数据资源的位置                       |
| Content-Range    | 当前传输的实体数据在整个资源中的字节范围 |
| Content-Type     | 实体数据的类型                           |
| Expires          | 实体数据的有效期                         |
| Last-Modified    | 实体数据上次被修改的时间及日期           |

**Allow**: 告知客户端服务器所能够支持的请求方法，当服务器接受不到支持的请求方法会以 405 Methed Not Allow 作为响应返回

**Content-Length**： 单位是字节

注意：对实体数据进行编码传输时，不适用 `Content-Length` 头

**Content-Location**: 当客户端请求的资源在服务器有多个地址时，服务器可以通过 Content-Location 字段告知客户端其他可选地址。

**Content-Range**: 主要用于针对范围请求，即告知客户端返回响应实体的那个部分复合范围请求。字段值以字节为单位

例如：`Content-Range:bytes 800-5000/67589`

**Expires**: 如果响应头 Cache-Control 设置了 max-age,该参数则可以忽略。

###### 5. HTTP 响应正文

HTTP 响应正文或 HTTP 响应实体主体指服务器返回的一定格式的数据。

##### 7. 网络抓包

抓包（Packet Capture）是指对网络传输中发送与接受的数据包进行截获、重发、编辑和转存等操作。

##### 8. 网络抓包的使用情景

- 当使用程序直接请求 URL 无法找到网页中数据（多为 JSON）, 则需要采取网络抓包措施，获取数据对应的真实的 URL
- 执行表单请求时，需要采取网络抓包措施。例如模拟登录网站才能获取的数据
- 在将捕获请求信息添加到程序中。如利用网络抓包一些浏览器请求网页的头信息和请求方法

URL 中处理中文：

```java
        try {
            String encode = URLEncoder.encode("张三", "gbk");
            System.out.println(encode);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
```

