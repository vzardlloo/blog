---
title: SpringMVC工作流程和Demo示例
date: 2017-08-24 22:46:44
tags: web Java SpringMVC
categories: JAVA 
---
SpringMVC是Spring下的一个模块，与Spring自然无缝衔接，相比Structs2我更喜欢SpringMVC。这篇博客介绍一下SpringMVC的基本工作流程最后会附上一个小demo可供演示。
先上一张图：
![](http://oo3aq3ac8.bkt.clouddn.com/SpringMVC.png)
这张图展示了SpringMVC大致的工作流程，下面开始逐步讲解：
1. 用户通过浏览器发送一个Http给Web应用。
2. 如果该请求与web.xml中DisPatcherServlet的配置匹配，则将该请求交由DispatcherServlet处理。
3. DispatcherServlet根据请求的信息URL、HTTP处理器等信息以及HandlerMapping的配置找到处理请求的Handler(一般是Controller)。
4. 找到Handler后，通过HandlerAdapter对Handler进行封装，使用统一的接口来调用Handler.
5. 在Handler完成业务处理之后返回一个ModelAndView给DispatcherServlet,ModelAndView，顾名思义，其中包含一个逻辑视图名和模型数据信息。
6. 然后DisPatcherServlet调用ViewResolver完成逻辑视图名到真实的视图对象的解析。
7. 当得到真实的视图对象View后，DispatcherServlet会用这个View对ModelAndView中的视图对象进行渲染。
8. 最终返回客户端一个Http相应，可能是HTML页面，也可能是一个图片等。

下面做一个demo来实践一下整个过程：
<!--more-->
+ 先用IDEA创建一个空的的maven工程(也可以new->project->maven->勾选create from archetype->选中webapp，但感觉有问题)。然后按照web应用目录结构手动创建一个webapp及其子目录。
+ 在pom.xml中添加依赖，最终结果如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>SpringMVC-Demo</groupId>
    <artifactId>springmvc-demo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>


        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>


    <packaging>war</packaging>


</project>

```

注意要添加jstl依赖，否则会报错。

+ 配置web.xml和xxx-servlet.xml文件，配置如下：
web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <display-name>Springmvc-demo</display-name>

    <servlet>
        <servlet-name>vzard</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>vzard</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    <!--解决中文post乱码-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

vzard-servlet.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--指明 controller 所在包，并扫描其中的注解-->
    <context:component-scan base-package="org.vzard.Controller"/>

    <!-- 静态资源(js、image等)的访问 -->
    <mvc:default-servlet-handler/>

    <!-- 开启注解 -->
    <mvc:annotation-driven/>

    <!--ViewResolver 视图解析器-->
    <!--用于支持Servlet、JSP视图解析-->
    <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>


</beans>
```
这里要注意的是还要添加一个applicationContext.xml文件，这个文件是Spring的全局配置文件，亲测不配置会报错，里面可以不配置东西，但是这个文件一定要有。

applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

</beans>
```

+ 然后就是业务逻辑分析部分，这个demo要做的事是：开启应用-->进入欢迎页，点击注册-->进入注册页面，输入信息,点击提交-->进入注册成功页面，显示用户信息
+ 业务逻辑实现：
UserController类：
```java
package org.vzard.Controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;
import org.vzard.domain.User;
import org.vzard.service.UserService;


/**
 * Created by V-zar on 2017/8/25.
 */
@Controller

public class UserController {
    // @Autowired(required = false)
    //UserService userService;

    @RequestMapping("/")
    public String index() {
        return "index";
    }

    @RequestMapping("/adduser")
    public String addUser() {
        return "adduser";
    }


    @RequestMapping(value = "/success")
    public ModelAndView register(User user) {
        //userService.creatUser(user);
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("success");
        modelAndView.addObject("user", user);
        return modelAndView;
    }

}

```

User类：
```java

package org.vzard.domain;

/**
 * Created by V-zar on 2017/8/25.
 */
public class User {
    public String userName;
    public String password;
    public String realName;

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getRealName() {
        return realName;
    }

    public void setRealName(String realName) {
        this.realName = realName;
    }
}

```

前端界面引入了Bootstrap（然并卵，还是巨丑，懒得写界面）：
index.jsp
```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>SpringMVC Demo 首页</title>

    <!-- 新 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
    <script src="//cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
    <script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
</head>
<body>
<h1>WELCOME</h1>
<form method="post" action="<c:url value="/adduser"/>">

    <button class="btn btn-default" type="submit">
        <span>注册</span>
    </button>
</form>

<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
</body>
</html>
```

adduser.jsp
```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>SpringMVC Demo 首页</title>

    <!-- 新 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
    <script src="//cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
    <script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
</head>
<body>
<form method="post" action="<c:url value="/success"></c:url> ">
    <div class="input-group">
        <span class="input-group-addon">用户名</span>
        <input type="text" name="userName" class="form-control" aria-label="Amount (to the nearest dollar)">
    </div>
    <div class="input-group">
        <span class="input-group-addon">密码</span>
        <input type="text" name="password" class="form-control" aria-label="Amount (to the nearest dollar)">
    </div>
    <div class="input-group">
        <span class="input-group-addon">姓名</span>
        <input type="text" name="realName" class="form-control" aria-label="Amount (to the nearest dollar)">
    </div>
    <button type="submit" class="btn btn-default" aria-label="Right Align">提交</button>
</form>
<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
</body>
</html>
```

success.jsp
```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
    <title>SpringMVC Demo 首页</title>

    <!-- 新 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css">

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
    <script src="//cdn.bootcss.com/html5shiv/3.7.2/html5shiv.min.js"></script>
    <script src="//cdn.bootcss.com/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
</head>
<body>
<h1>欢迎，${user.userName}</h1>
<h1>您的密码是：${user.password}</h1>
<h1>您的真实姓名是：${user.realName}</h1>

<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>

<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
<script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>
</body>
</html>
```

运行结果：
![](http://oo3aq3ac8.bkt.clouddn.com/spmvc01.png)

![](http://oo3aq3ac8.bkt.clouddn.com/spmvc02.png)

![](http://oo3aq3ac8.bkt.clouddn.com/spmvc03.png)

完整demo可在我的github上clone运行：[Demo](https://github.com/vzardlloo/SpringMVC-learning)
运行环境&工具：
+ IDEA
+ JDK 1.8
+ Tomcat 8.5
















