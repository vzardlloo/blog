---
title: springboot学习之HelloWorld篇
date: 2017-06-28 23:22:23
categories: springboot
---

最近开始学习springboot,然后将在博客上记录和总结学习的过程。这篇主要是讲如何搭建springboot的开发环境，并且完成HelloWorld程序，使用的开发工具是IDEA.
  springboot官网上对springboot的介绍如下：
```
Spring Boot makes it easy to create stand-alone, production-grade Spring 
based Applications that you can "just run". We take an opinionated view 
of the Spring platform and third-party libraries so you can get started 
withminimum fuss. Most Spring Boot applications need very little Spring 
configuration.
```
主要意思就是说springboot只需要很少的配置便可以创建可“运行”的独立的、生产级的基于Spring的应用程序。
springboot的主要特征为：
+ 创建独立的Spring应用程序
+ 直接嵌入Tomcat，Jetty或Undertow（不需要部署WAR文件）
+ 提供有意思的“启动”POM来简化您的Maven配置
+ 尽可能自动配置
+ 提供生产就绪功能，如指标，运行状况检查和外部化配置
+ 绝对没有代码生成，也不需要XML配置

下面我们开始写第一个springboot的HelloWorld程序：
一、新建一个Spring Initializr项目
![新建一个Spring Initializr项目](http://oo3aq3ac8.bkt.clouddn.com/001.png)
二、填写Group和Artifact名称
![填写Group和Artifact名称](http://oo3aq3ac8.bkt.clouddn.com/002.png)
三、在Dependensencise里勾选上Web
    在HelloWorld阶段只需勾选Web,之后还需增加别的依赖
![勾选上Web](http://oo3aq3ac8.bkt.clouddn.com/003.png)
然后一直点下一步，会自动生成项目初始的结构，如下图所示：
![](http://oo3aq3ac8.bkt.clouddn.com/004.png)
然后新建一个包，可以命名为web,用来放置控制器，编写HelloWorldController，代码如下：
```java
package org.vzard.springboot.web;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created by V-zar on 2017/6/28.
 */

@RestController
public class HelloWorldController {
    @RequestMapping
    public String sayHello(){
        return "HelloWorld";
    }

}

```
最终结构如图：
![](http://oo3aq3ac8.bkt.clouddn.com/005.png)
然后点击运行，等待运行成功，打开浏览器输入`http://localhost:8080`，如果看到如下图所示，即表明成功：
![](http://oo3aq3ac8.bkt.clouddn.com/006.png)

*注：如果失败，可以查看代码及依赖是否有误，和8080端口是否被占用，具体其他问题查看报错信息分析。*


