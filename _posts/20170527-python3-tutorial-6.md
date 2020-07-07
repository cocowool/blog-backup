---
title: Python3.6学习笔记（六）
date: 2017-05-27 17:22
tag: 
---


### WSGI Python Web Server Gateway Interface 规范学习
由于Python的灵活性，提供了多种方式可以作为服务端语言，包括Python编写的服务器（Medusa）、Python处理模块（mod_python)，或者使用CGI、FastCGI方式触发Python脚本。 为了能够编写更通用的Web端程序，提出了WSGI接口作为标准接口规范，类似于Java中的Serverlet，一旦编写完成后，可以运行在不同的App框架中。
WSGI接口涉及两个方面：一面是：服务器(server)或网关(gateway)，相对别一面是：应用程序(application)或框架(framework)。服务端运行由应用程或框架提供的可执行的对像实例，至于这个可执行对象的是如果获得的细节，不在WSGI规范定义之内，而是同server或gataway去处理。

#### Application/Framework 端
Application端是一个callable term，可以是function、class、method等，接收两个参数environ、start_response。当application被server调用时，必须返回一个iterable的bytestrings或者是zero（可以使用yield返回一个生成器）。
> WSGI 是为框架或服务器开发人员提供的工具，而不是为应用人员提供的。

> When called by the server, the application object must return an iterable yielding zero or more byte strings.

服务器调用时，应当以无缓存的形式将产生的内容发送给客户端。
方法：**len**()、close()

#### Server/Gateway 端

#### 中间件 Middleware 扮演两个角色
Middleware常扮演以下角色：

* 根据目标URL将请求路由到不同的Application进行处理
* 允许多个Application或Framework运行在相同的进程中
* 通过网络内的请求转发实现负载均衡和远程处理
* 实现内容的后续处理，例如XSL样式表
中间件的存在对于服务端和应用端应该都是透明的。

#### environ 变量
environ 是一个字典变量。

CGI方式需要提供的参数略有不同，具体可以参考[PEP3333](https://www.python.org/dev/peps/pep-3333/)
> A WSGI-compliant server or gateway should document what variables it provides, along with their definitions as appropriate. Applications should check for the presence of any variables they require, and have a fallback plan in the event such a variable is absent.


#### Input、Error流
服务器端必须支持以下几个方法


#### start_response()
方法示例```start_response(status, response_headers, exec_info = None)```。
start_response 接收两个参数```start_response(status, response_headers)```，status是状态返回信息，诸如“200 OK”或者“404 Not Found”，纯文本，不能包含任何控制符号。response_headers是一个形如(header_name, header_value)的tuples，必须是Python的List。header_name必须是RFC2616中定义的名称，header_value不包含结束符号及任何控制符号，包括换行等。
一般来说，服务器端负责确保发送的header的正确性，如果应用忽略了某个http头参数，那么服务器应该给补充进去。
服务端应该检查是否向客户端发送了保持链接的头参数，如果发现，应该抛出错误。

#### Content-Length 头的处理
如果应用端提供了 Content-Length 请求头，服务端不应当传递超过这个长度的内容。处理方式是停止发送内容，或产生一个报错。如果没有提供足够的内容，则应正常关闭链接不产生错误。
如果没有提供 Content-Length 头，则服务端可以自己决定采用哪种处理方式，最简单的就是响应结束后关闭链接。某些情况下，服务端可以自己产生 Content-Length , 或者尽量避免关闭链接。如果服务端和客户端都支持 HTTP/1.1 分块编码，则服务端需要为每个块提供一个 Content-Length。

#### 缓存和流处理 Buffering and Streaming

#### write() callable
一些编程框架提供了缓存的 write() 函数以及一个 flush() 函数，用于刷新缓存，但是很遗憾标准的WSGI无法实现这个需求。但WSGI仍提供了一个特殊 write() 函数，来实现这些迫切的需求。
write() 由 start_response 返回，接收一个参数。一个应用必须返回一个 iterable 对象。

#### 错误处理












