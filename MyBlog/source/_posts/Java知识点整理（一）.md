---
title: Java知识点整理（一）
date: 2017-05-09 23:33:56
categories: JAVA
---
+ getParameter()是获取POST/GET传递的参数值；
  getInitParameter获取Tomcat的server.xml中设置Context的初始化参数
  getAttribute()是获取对象容器中的数据值；
  getRequestDispatcher是请求转发。
+ HttpServlet是GenericServlet的子类。
  GenericServlet是个抽象类，必须给出子类才能实例化。它给 出了设计servlet的一些骨架，定义了servlet生命周期，还有一些得到名字、配置、初始化参数的方法，其设计的是和应用层协议无关的，也就是说 你有可能用非http协议实现它。
  HttpServlet是子类，当然就具有GenericServlet的一切特性，还添加了doGet, doPost, doDelete, doPut, doTrace等方法对应处理http协议里的命令的请求响应过程。
  一般没有特殊需要，自己写的Servlet都扩展HttpServlet 。 


