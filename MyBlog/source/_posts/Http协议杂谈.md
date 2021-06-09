---
title: Http协议杂谈
date: 2017-09-19 16:42:49
tags: JAVA
categories: JAVA
---
#### HTTP的特性
+ HTTP构建于TCP/IP协议之上，默认端口号是80
+ HTTP是无连接无状态的

#### HTTP报文
Http协议是以ASCII码传输，建立在TCP/IP协议之上的应用层规范。规范把HTTP请求分为三个部分：状态行、请求头、消息主题。类似于下面这样：
```
GET /en-US/docs/Web/HTTP/Headers/Content-Disposition HTTP/1.1
Host: developer.mozilla.org
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.113 Mobile Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: https://hit-alibaba.github.io/interview/basic/network/HTTP.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.8
Cookie: csrftoken=0QVKqjybZR4MatNi0vCP03XIDrEWODO9; dwf_section_edit=True; dwf_sg_task_completion=False; _ga=GA1.2.817713072.1498834189; _gid=GA1.2.1652928103.1505806097
If-None-Match: "76da2643e0174c564b0a898793e76edda496498f"
```

<!--more-->
HTTP定义了于服务器交互的不同方法，最基本的方法有4中，分别是`GET`,`POST`,`PUT`,`DELETE`。`URL`全称是资源描述符，我们可以这样认为：一个URL地址，它用于
描述一个网络上的资源，而HTTP中的`GET`,`POST`,`PUT`,`DELETE`就对应着对这个资源的查，增，改，删4个操作。
1. GET用于信息获取，而且应该是安全的 和 幂等的。
所谓安全的意味着该操作用于获取信息而非修改信息。换句话说，GET 请求一般不应产生副作用。就是说，它仅仅是获取资源信息，就像数据库查询一样，不会修改，增加数据，不会影响资源的状态。
幂等的意味着对同一URL的多个请求应该返回同样的结果。

GET请求报文示例：
```
GET /books/?sex=man&name=Professional HTTP/1.1
 Host: www.example.com
 User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
 Gecko/20050225 Firefox/1.0.1
 Connection: Keep-Alive
```

2. POST表示可能修改变服务器上的资源的请求。

```
 POST / HTTP/1.1
 Host: www.example.com
 User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
 Gecko/20050225 Firefox/1.0.1
 Content-Type: application/x-www-form-urlencoded
 Content-Length: 40
 Connection: Keep-Alive

 sex=man&name=Professional
```

3. 注意：
   + GET 可提交的数据量受到URL长度的限制，HTTP 协议规范没有对 URL 长度进行限制。这个限制是特定的浏览器及服务器对它的限制
   + 理论上讲，POST 是没有大小限制的，HTTP 协议规范也没有进行大小限制，出于安全考虑，服务器软件在实现时会做一定限制
   + 参考上面的报文示例，可以发现 GET 和 POST 数据内容是一模一样的，只是位置不同，一个在URL里，一个在 HTTP 包的包体里
   

#### POST提交数据的方式
HTTP 协议中规定 POST 提交的数据必须在 body 部分中，但是协议中没有规定数据使用哪种编码方式或者数据格式。实际上，开发者完全可以自己决定消息主体的格式，只要最后发送的 HTTP 请求满足上面的格式就可以。

但是，数据发送出去，还要服务端解析成功才有意义。一般服务端语言如 php、python 等，以及它们的 framework，都内置了自动解析常见数据格式的功能。服务端通常是根据请求头（headers）中的 Content-Type 字段来获知请求中的消息主体是用何种方式编码，再对主体进行解析。所以说到 POST 提交数据方案，包含了 Content-Type 和消息主体编码方式两部分。下面就正式开始介绍它们：
>application/x-www-form-urlencoded

这是最常见的POST数据提交方式。浏览器的原生<form>表单，如果不设置entype属性，那么最终就会以`application/x-www-form-urlencoded`方式提交数据。上个小节当中的例子便是使用了这种提交方式。可以看到 body 当中的内容和 GET 请求是完全相同的。

#### 响应报文
HTTP 响应与 HTTP 请求相似，HTTP响应也由3个部分构成，分别是：
+ 状态行
+ 响应头
+ 响应正文

状态行由协议版本、数字形式的状态代码、及相应的状态描述，各元素之间以空格分隔。

常见的状态码有如下几种：
+ `200 OK` 客户端请求成功
+ `301 Moved permanently` 请求永久重定向
+ `302 Moved Temporarily` 请求临时重定向
+ `304 Not Modified` 文件未修改，可以直接使用缓存的文件
+ `400 Bad Request` 由于客户端请求有语法错误，不能被服务器所理解
+ `401 Unauthorized`请求未经授权。这个状态代码必须和WWW-Authenticate报头域一起使用
+ `403 Forbidden` 服务器收到请求，但是拒绝提供服务。服务器通常会在响应正文中给出不提供服务的原因
+ `404 Not Found` 请求的资源不存在，例如，输入了错误的URL
+ `500 Internal Server Error` 服务器发生不可预期的错误
+ `503 Server Unavailable` 服务器当前不能处理客户端的请求，一段时间后可能恢复正常


下面是一个HTTP响应的例子：
```
HTTP/1.1 200 OK

Server:Apache Tomcat/5.0.12
Date:Mon,6Oct2003 13:23:42 GMT
Content-Length:112

<html>...
```










