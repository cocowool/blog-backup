---
title: 如何用 Java 实现 HTTP 请求的解析
date: 2025-01-22 17:20:02
keywords: Java, HTTP 解析, Java Proxy, Java HTTP 代理, HTTP Proxy
description: 最近在用 Java 实现一个简单的HTTP代理服务器，过程中发现Java世界提供的标准HTTP解析库真是太少了，国外同行也有一个吐槽的，将其文章编辑整合发布与大家探讨。
---

最近想自己实现一个 HTTP 代理服务器，查阅资料和编写过程中，发现 Java 对比 Python，不太容易找到成熟的 HTTP 请求处理包。不要说 Netty、Jetty 这些，只是想单纯的能够处理原始的 HTTP 请求，解析出里面的 Host、Request URI、Header、Cookie 等信息。正好看到有位外国朋友也做了类似的探索，将他的文章翻译转载到此处，原文可以看 [Http Message Parsing in Java Web Servers](https://stevenyue.com/blogs/http-message-parsing-in-java-web-servers/)。

解析来自客户端的请求消息是构建 Web 服务器的开始。 典型请求可能如下所示：GET
```http
GET /steventen HTTP/1.1\r\n
Host: github.com
Accept-Language: en-us
Accept: text/json
User-Agent: curl/7.16.3
Cookie: a=123; b=456;\r\n\r\n
```

HTTP 请求的语法和内容遵循 [RFC 7230](https://datatracker.ietf.org/doc/html/rfc7230) 和 [RFC 7231](https://www.rfc-editor.org/rfc/rfc7231) 两个规范。当 Web 服务器收到消息时，它需要解析或解码其中的信息（路径、标头等），然后再启动接下来的过程，如路由、代理、身份验证等。

我花了一些时间寻找可以解析上述原始 HTTP 的标准 Java 库，希望支持将请求消息从字节或字符串到 Java 对象中，方便我编写程序访问 Header、Cookie 等信息。 我以为这是一个很常用的功能，以至于必须有一个标准库。 相反，在阅读了大多数用 Java 编写的流行的 Web 服务器库（如 Tomcat、Jetty、Netty 、Undertow 等）的源代码后，我发现每个人都实现了一个 HTTP 解析器。此外，当我查看每个实现时，我发现它们都不能作为独立使用。 每个库都需要除原始 HTTP 消息本身之外的其他参数。

以下部分展示了 HTTP 消息配对/解码在那些用 Java 编写的 Web 服务器库中的样子。 并描述了如何“使用”每个库中的 Http 解析器。

## Jetty

在 Jetty 中，HttpParser 负责处理 HTTP 消息解析。然而，它需要一个 HttpHandler，具体示例如下：
```java
ByteBuffer buffer = BufferUtil.toBuffer(
            "GET / HTTP/1.1\r\n" +
                "Host: localhost\r\n" +
                "Name: value\r\n\r\n");
HttpParser.RequestHandler handler = new Handler();
HttpParser parser = new HttpParser(handler);
```

## Undertow

主要的处理逻辑在 HttpRequestParser 中，代码如下：
```java
byte[] in = "GET /somepath%2fotherPath HTTP/1.1\r\n\r\n".getBytes();
final ParseState context = new ParseState(10);
HttpServerExchange result = new HttpServerExchange(null);
HttpRequestParser.instance(OptionMap.create(UndertowOptions.ALLOW_ENCODED_SLASH, true)).handle(ByteBuffer.wrap(in), context, result);
// get method
result.getRequestMethod()
// get path
result.getRequestPath()
```

可以看到，解析时必须依赖 ParseState 类。

## Netty

Netty 的主要实现代码在 HttpRequestDecoder 中，示例代码如下：
```java
String crlf = "\r\n";
String request =  "GET https://github.com/steventen HTTP/1.1" + crlf +
    "Host: github.com" + crlf +
    "Accept-Language: en-us" + crlf +
    "Accept: text/json" + crlf +
    "Accept-Encoding: gzip, deflate, br" + crlf +
    "Cookie: a=123; b=456;c=wfhhnchauhd"
    + crlf + crlf;

EmbeddedChannel channel = new EmbeddedChannel(new HttpRequestDecoder());
channel.writeInbound(Unpooled.copiedBuffer(request, CharsetUtil.US_ASCII));
HttpRequest req = channel.readInbound();
req.headers();
req.method();
req.uri();
Set<Cookie> cookies = ServerCookieDecoder.LAX.decode(req.headers().get("Cookie"));
```

比起其他两个来，可以在工程中引用 Netty 的 EmbeddedChannel 实现解析，但是需要注意性能问题。

## Tomcat

这一唯一一个无法通过阅读源码来了解其工作原理的库。

## 我的实现

总结下来，发现 Java 世界中确实没有一个比较轻量的 HTTP 解析器。作者的这篇文章发表于 2021年，而且是他对国外技术栈考察的结果。

在这段时间，结合使用搜索工具与豆包等AI工具，我整理了一段代码，可以实现监听8080端口，并将HTTP GET请求转发到后端服务器，获取服务器响应后发送给客户端。

实现了基本的HTTP头解析逻辑，支持提取Host、URI、Port信息，欢迎大家阅提意见。

```java
import java.io.*;
import java.net.*;
import java.util.concurrent.*;

/**
 * 
 * 2024-11-14 对于telnet发起的简单http请求能够正常返回，不支持 postman 请求
 * 2024-11-22 已支持 Postman 发 http 请求，不能正常响应重复请求
 * 
 * @TODO 后续考虑使用 HttpClient 库 / 或者使用 Netty 
 * 
 * 问题：
 * 1. 不能持续响应一个客户端的连续请求
 * 
 */
public class HttpProxyServer {
    private static final int THREAD_POOL_SIZE = 10;
    private static final ExecutorService executorService = Executors.newFixedThreadPool(THREAD_POOL_SIZE);

    public static void main(String[] args) {
        int port = 8080; // HTTP代理端口

        // @TODO 解析用户自定义的端口参数
        try ( ServerSocket serverSocket = new ServerSocket(port) ) {
            //设置服务端与客户端连接未活动超时时间
            serverSocket.setSoTimeout(1000 * 60);
            System.out.println("Http Proxy Server listen at: " + port);

            final byte[] Request = new byte[1024];
            byte[] Reply = new byte[4096];

            while (true) {
                Socket proxySocket = null;
                Socket socket_client = null;

                try {
                    socket_client = serverSocket.accept();

                    final InputStream InputStreamClient = socket_client.getInputStream();
                    final OutputStream OutputStreamClient = socket_client.getOutputStream();

                    String requestHost = null;
                    int requestPort = 80;
                    try {
                        //@ 在这里要判断用户发送的请求地址和端口，建立 Socket 链接
                        BufferedReader client_reader = new BufferedReader(new InputStreamReader(InputStreamClient));
                        String remoteRequest = "";
                        String requestLine = client_reader.readLine();
                        // remoteRequest = "";
                        if( requestLine != null){
                            String[] parts = requestLine.split(" ");
                            if (parts.length >= 3) {
                                String requestMethod = parts[0];
                                String requestPath = parts[1];
                                String requestProtocol = parts[2];

                                if (requestPath.startsWith("http://")){
                                    requestPath = requestPath.substring(7);
                                }else if( requestPath.startsWith("https://")){
                                    requestPath = requestPath.substring(8);
                                }
                                int pathIndex = requestPath.indexOf('/');
                                if(pathIndex != -1){
                                    requestPath = requestPath.substring(pathIndex);
                                }
                                System.out.println("Request path : " + requestPath);
                                remoteRequest = requestMethod + " " + requestPath + " " + requestProtocol + "\r\n";

                                String line;
                                while((line = client_reader.readLine()) != null && !line.isEmpty()){
                                    if(line.startsWith("Host: ")){
                                        String[] hostParts = line.substring(6).split(":");
                                        requestHost = hostParts[0];
                                        if(hostParts.length > 1){
                                            requestPort = Integer.parseInt(hostParts[1]);
                                        }
                                    }
                                    remoteRequest += line + "\r\n";
                                }

                                // 输出提取的信息
                                System.out.println("Method: " + requestMethod);
                                System.out.println("Path: " + requestPath);
                                System.out.println("Protocol: " + requestProtocol);
                                System.out.println("Host: " + requestHost);
                                System.out.println("Port: " + requestPort);
                            }
                        }

                        remoteRequest += "\r\n";
                        System.out.println("Send Request to Remote Server:");
                        System.out.println(remoteRequest);

                        // 先手工写死请求的远端地址，实际需要从用户请求中解析出来
                        if( requestHost == null){
                            requestHost = "www.edulinks.cn";
                            requestPort = 80;    
                        }

                        proxySocket = new Socket(requestHost, requestPort);
                        final InputStream prxoyInputStream = proxySocket.getInputStream();
                        final OutputStream proxyOutputStream = proxySocket.getOutputStream();

                        proxyOutputStream.write(remoteRequest.getBytes());
                        proxyOutputStream.flush();

                        // proxySocket.shutdownOutput();

                        int Bytes_Read;
                        try {    
                            while ( (Bytes_Read = prxoyInputStream.read(Reply))!= -1){
                                System.out.println(Reply);
                                OutputStreamClient.write(Reply, 0, Bytes_Read);
                                OutputStreamClient.flush();
                            }
    
                        }catch(IOException e){
                            System.out.println("Get response Error !");
                            System.out.println(e);
                            e.printStackTrace();
                        }
                    }catch(IOException e){
                        System.out.println("Send request Error !");
                        System.out.println(e);
                    }

                    OutputStreamClient.close();
                } catch (IOException e) {
                    System.out.println("Proxy can not get response. ");
                    e.printStackTrace();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } 
    }
}


```

## 参考资料
1. [Http Message Parsing in Java Web Servers](https://stevenyue.com/blogs/http-message-parsing-in-java-web-servers/)
