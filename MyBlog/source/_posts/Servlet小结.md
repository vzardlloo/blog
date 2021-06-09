---
title: Servlet小结
date: 2017-08-21 21:19:08
tags: web Java
categories: JAVA
---
#### 前言
Servlet是JAVA Web的底层技术，有着很重要的地位。下面分几点简单总结下：
+ 1、Servlet家族
+ 2、Servlet--HelloWorld!
+ 3、Servlet生命周期

#### Servlet家族
![](http://oo3aq3ac8.bkt.clouddn.com/servletfamily.png)
上图是Servlet家族类图，其中可以看出HttpServlet继承自GenericServlet,GenericServlet继承自HttpServlet。它们的结构如下：
![servlet](http://oo3aq3ac8.bkt.clouddn.com/servlet.png "Servlet")
![GenericsServlet](http://oo3aq3ac8.bkt.clouddn.com/GenericServlet.png "GenericsServlet")
![HttpServlet](http://oo3aq3ac8.bkt.clouddn.com/httpservlet.png "HttpServlet")

可以看出Servlet的结构非常简单，仅仅定义了`init()`、`service`、`desdroy`三个控制生命周期的方法，GenericServlet对Servlet
进行了一些拓展，增加了`log()`、`getInitParameter()`等方法，HttpServlet对GenericServlet又进行了拓展，增加了Http协议的相关方法。
<!--more-->
#### Servlet--HelloWorld!
1. 在tomcat中新建一个web应用`hello`，在web应用中新建一个`WEB-INF/classes`目录。
2. 在`classes`目录新建一个`MyServlet`。

```
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServlet;
import java.io.IOException;
import java.io.OutputStream;

/**
 * Created by V-zar on 2017/8/21.
 */
public class MyServlet extends HttpServlet {

    public  void service(ServletRequest request, ServletResponse response) throws ServletException,IOException{

        OutputStream op = response.getOutputStream();
        op.write("HelloWorld!".getBytes());

    }

}

```

3. 编译，`javac -cp %CATALINA_HOME%/lib/servlet-api.jar -d . MyServlet.java`，参考[这篇博客](http://www.iitshare.com/under-the-cmd-compile-the-java.html )
4. 在`WEB-INF`目录中新建一个`web.xml`文件，配置`servlet`的对外访问路径。
5. 启动tomcat访问。

#### Servlet生命周期
先上一张图：
![](http://oo3aq3ac8.bkt.clouddn.com/javaweb_servlet-lifecycle.png)
解读：
1. 浏览器发送http请求到Servlet容器(假设为Tomcat)。
2. Tomcat根据请求找到对应的Web应用。
3. Web应用里的web.xml里的配置找到请求对应的Servlet,如：
```xml
<servlet>
        <servlet-name>mvc-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>mvc-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

4. 得到servlet.class的字符串内容。
5. Tomcat通过反射构造servlet对象。
6. Tomcat调用servlet构造方法，servlet对象执行构造方法。
7. Tomcat创建ServletConfig对象，并调用servlet的init()方法，servlet执行init()方法。
8. Tomcat创建request和response对象，并调用servlet的service()方法,servlet执行service()方法。
9. servlet返回修改过的response给Tomcat。
10. Tomcat将返回的response对象渲染成相应格式并发送给浏览器。
11. servlet调用destroy方法，servlet被销毁。

















