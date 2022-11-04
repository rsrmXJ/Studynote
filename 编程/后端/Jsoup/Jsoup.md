# Jsoup

##### 1\. 什么是 Jsoup?

Jsoup 是一款基于 Java 语言的开源项目，主要用于请求 URL 获取网页的内容、解析 HTML 和 XML 文档。使用 Jsoup 可以构建一些轻量级爬虫项目。

Jsoup 常用方法：详看文档

| 返回类型            | 方法                                     | 描述                   |
| ------------------- | ---------------------------------------- | ---------------------- |
| Connection.Response | excute()                                 | 执行请求               |
| Connection          | connect(String url)                      | 创建到 URL 的连接      |
| Document            | **[get](#get())**()                      | 以 get 的形式执行请求  |
| Connection          | **[timeout](#timeout(int))**(int millis) | 设置请求的总超时时间。 |
|                     |                                          |                        |
|                     |                                          |                        |

##### 2\. 下载 jar 包

pom.xml 配置

```xml
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>${jsoup.version}</version>
</dependency>
```

##### 3\. 请求 URL

**第一步** 使用抓包工具，分析请求的 URL、方法、请求头、响应头等信息。

使用 Jsoup 获取和解析网页

```Java
////创建到 URL 的连接
Connection jsoup = Jsoup.connect("https://www.bilibili.com/");
Document docuemnt = jsoup.get();//以GET的形式执行请求，并解析结果。
//可以使用 Element.html() 方法将 Document 类型的对象转换为 String 类型
```

使用 Jsoup 获取相应的 response，然后我们根据 reponse 做出相应的处理（获取 HTML 内容）

```java
Connection connection = Jsoup.connect("https://www.baidu.com/");
Connection.Response execute = connection.method(Connection.Method.GET).timeout(60 * 10 * 1000).execute(); 
System.out.println("请求的 URL 为" + execute.url());
System.out.println("相应状态码为" + execute.statusCode());
System.out.println("相应类型为" + execute.contentType());
System.out.println("响应信息为" + execute.statusMessage());
if(execute.statusCode() == 200){
    //做出相应的处理
}
```

##### 3\. 设置头信息

设置头信息的作用：伪装网络爬虫，使网络爬虫请求网页更像浏览器访问网页，进而降低了网络爬虫被网站封锁的风险。

Jsoup Connection 中两种设置头信息的方法： 

- header(String name,String value) 每次可以设置一个请求头，如果设置多个请求头，需要多次调用此方法。
- headers(Map<String,String> headers)添加多个头至 Map 集合中

使用第一种方法设置一个请求头

```java
Connection connect = Jsoup.connect("https://searchcustomerexperience.techtarget.com/info/news");
		//设置单个请求头
		Connection conheader = connect.header("User-Agent","Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36");
```

设置多个请求头：

```java
Connection connect = Jsoup.connect("https://searchcustomerexperience.techtarget.com/info/news");
		//设置多个请求头。头信息保存到Map集合中
		Map<String, String> header = new HashMap<String, String>();
		header.put("Host", "searchcustomerexperience.techtarget.com");
		header.put("User-Agent", " Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.108 Safari/537.36");
		header.put("Accept", "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8");
		header.put("Accept-Language", "zh-cn,zh;q=0.5");
		header.put("Accept-Encoding", "gzip, deflate");
		header.put("Cache-Control", "max-age=0");
		header.put("Connection", "keep-alive");
		Connection conheader = connect.headers(header);
```

创建静态 referer 和 User-Agent 库

```Java
public class Builder {
    String[] userAgentStrs = {
            "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_8; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50",
            "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-us) AppleWebKit/534.50 (KHTML, like Gecko) Version/5.1 Safari/534.50",
            "Mozilla/5.0 (Windows NT 10.0; WOW64; rv:38.0) Gecko/20100101 Firefox/38.0",
            "Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; .NET4.0C; .NET4.0E; .NET CLR 2.0.50727; .NET CLR 3.0.30729; .NET CLR 3.5.30729; InfoPath.3; rv:11.0) like Gecko",
            "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)",
            "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0)",
            "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)",
            "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.66 Safari/537.36"};
    List<String> userAgentList = Arrays.asList(userAgentStrs);
    int userAgentSize = userAgentList.size();
    //设置referer库;读者根据需求添加更多referer
    String[] refererStrs = {"https://www.baidu.com/",
            "https://www.sogou.com/",
            "http://www.bing.com",
            "https://www.so.com/",
            "https://www.google.com/"};
    List<String> refererList = Arrays.asList(refererStrs);
    int refererSize = refererList.size();
    //设置Accept、Accept-Language以及Accept-Encoding
    String accept = "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8";
    String acceptLanguage = "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6";
    String acceptEncoding = "gzip, deflate, br";
    String host;
    String connection = "keep-alive";

}
```

使用上面的类：

```java
        Connection connect = Jsoup.connect("https://www.taobao.com/");
        Builder builder = new Builder();
        Map<String, String> headers = new HashMap<>();
        headers.put("Host", url);
        headers.put("User-Agent",
                builder.userAgentList.get(new Random().nextInt(builder.userAgentSize)) );
        headers.put("Accept", builder.accept);
        headers.put("Referer", builder.refererList.get(new Random().nextInt(builder.refererSize)));
        headers.put("Accept-Language", builder.acceptLanguage);
        headers.put("Accept-Encoding", builder.acceptEncoding);
        headers.put("Connection",builder.connection);
```

##### 4\. 提交请求参数 

网页提交表单数据，涉及一系列请求参数。

- GET 请求的参数，是通过 URL 传递的。格式：`?key1=value1&key2=value2`
- POST 请求的参数，通常是放在 POST 请求的消息体中，格式一般为 JSON

中方法：data 详看文档

```java
//第一个
nnection connect = Jsoup.connect("http://www.*****.com/ems.php");
		//添加参数
		connect.data("wen","EH629625211CS").data("action", "ajax");
//第二个
Connection connect = Jsoup.connect("http://www.*****.com/ems.php");
		//需要提交的参数
		Map<String, String> data = new HashMap<String, String>();  
	    data.put("wen", "EH629625211CS");  
	    data.put("action", "ajax");  
	    //获取响应
	    Response response = connect.data(data).method(Method.GET).ignoreContentType(true).execute(); 
//第三个
		Connection connect = Jsoup.connect("http://www.*****.com/ems.php");
		//添加参数
		connect.data("wen", "EH629625211CS", "action", "ajax");
		Response response = connect.method(Method.GET).ignoreContentType(true).execute();  
```

5\. 代理服务器的使用

代理服务器（Proxy Server）是网络上提供转接功能的服务器。

> 代理服务器介于客户端和 Web 服务器之间的另一台服务器，基于代理服务器，浏览器不再直接从 Web 服务器获取数据，而是向代理服务器发出请求，由代理服务器去留所需要的信息。

好处：能够高度隐藏爬虫的真实 IP 地址，从而防止网络爬虫被服务器封锁。另外，使用固定 IP 请求时，往往需要设置随机休息时间，而通过代理服务器则不需要，进而提高了采集效率。

- 免费提供代理的 IP 地址稳定性差。
- 付费获取的商业级代理，提供的代理 IP 地址可使用率高，稳定性强。

两种设置代理服务器的方式：

- proxy(Proxy proxy)
- proxy(String honst,int port)

在实际应用中需要构建代理服务器库，不断地切换代理服务器取请求 URL。

```java
//第一种
Proxy proxy = new Proxy(Proxy.Type.HTTP, new InetSocketAddress("122.5.108.21", 9999));  
		Connection connection = Jsoup.connect("https://searchcustomerexperience.techtarget.com/info/news").proxy(proxy);
//第二种
Connection connection = Jsoup.connect("https://searchcustomerexperience.techtarget.com/info/news")
				.proxy("163.204.240.107",9999);
		Response response = connection.method(Method.GET).timeout(20*1000).execute();
```

##### 5. 响应输出流

使用 Jsoup 下载图片、PDF、压缩文件时，需要将相应转换为输出流。从而增强文件的读写能力。

##### 6. HTTPS 认证

HTTPS 是在 HTTP 的基础上加入了 SSL(安全套接层)。SSL 的作用保障网络通信的安全性，应用于客户端与服务器之间的身份认证和加密数据传输。SSL 支持双向认证（服务器认证与客户端认证），将服务器证书下载到客户端，再将客户端的证书返回到服务器。目前，访问网站并不常用客户端证书，因为大多数用户都没有自己的客户端证书。但 HTTPS 总要求使用客户端证书。其中使用最多的是 X.509 证书

```java
private static void initUnSecureTSL()  {
        // 创建信任管理器(不验证证书)
        final TrustManager[] trustAllCerts = new TrustManager[]{new X509TrustManager() {
            //检查客户端证书
            public void checkClientTrusted(final X509Certificate[] chain, final String authType) {
                //do nothing 接受任意客户端证书
            }
            //检查服务器端证书
            public void checkServerTrusted(final X509Certificate[] chain, final String authType) {
                //do nothing  接受任意服务端证书
            }
            //返回受信任的X509证书
            public X509Certificate[] getAcceptedIssuers() {
                return null; //或者return new X509Certificate[0];
            }
        }};
        try {
            // 创建SSLContext对象,并使用指定的信任管理器初始化
            SSLContext sslContext = SSLContext.getInstance("SSL");
            sslContext.init(null, trustAllCerts, new java.security.SecureRandom());
            ////基于信任管理器，创建套接字工厂 (ssl socket factory)
            SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();
            //给HttpsURLConnection配置SSLSocketFactory
            HttpsURLConnection.setDefaultSSLSocketFactory(sslSocketFactory);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

##### 7. 大文件内容获取问题

默认情况 Jsoup 只能获取大小为 1 M 的文件，直接使用 Jsoup 获取大文件时，将导致内容获取不全。我们可以通过使用 `maxBodySize(int byte)` 设置请求文件的大小。

`Connection conn = Jsoup.connect().maxBodySize(Integer.MAX_VALUE)`

##### 8\. Jsoup 解析 HTML

Jsoup 中的三个概念：

- 节点 （Node）：HTML 文件中所包含的内容都可以看作成一个节点。节点有很多种类性，如属性节点（Attribute）、注释节点（Note）、文本节点（Text）、和元素节点（Element）等。
- Element 元素：节点的子集，所以一个元素也是一个节点。
- Docuement 文档：整个 HTML 文档的内容。

解析静态 HTML 文件

```java
        //HTML 静态文件
        String html = "<html><body><div id=\"w3\"> <h1>浏览器脚本教程</h1> <p><strong>从左侧的菜单选择你需要的教程！</strong></p> </div>"
                + "<div>  <div id=\"course\"> <ul> <li><a href=\"/js/index.asp\" title=\"JavaScript 教程\">JavaScript</a></li> </ul> </div> </body></html>";
        //转化成Document
        Document doc = Jsoup.parse(html);
        //基于 CSS 选择器获取元素,也可写成 [id=w3],通过 get() 获取
        Element element = doc.select("div[id=w3]").get(0);
        System.out.println("输出解析的元素内容为:");
        System.out.println(element);
        //从 Element 提取内容(抽取一个Node对应的信息)，text() 方法获取文本
        String text1 = element.select("h1").text();
        //从Element提取内容(抽取一个Node对应的信息)
        String text2 = element.select("p").text();
        System.out.println("抽取的文本信息为:");
        System.out.println(text1 + "\t" + text2);
```

