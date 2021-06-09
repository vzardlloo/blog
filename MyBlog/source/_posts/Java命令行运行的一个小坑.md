---
title: Java命令行运行的一个小坑
date: 2017-06-04 16:57:38
categories: JAVA
---

当我们开始学习JAVA时，第一件事无疑是配置JAVA的开发环境，通常来说包含安装JDK和配置环境变量这两个步骤。安装JDK略过不提，在配置环境变量的时候，很多资料一般都是这么说的:
+ JAVA_HOME--->JDK安装根目录
+ PATH增加%JAVA_HOME%\bin
+ CLASS_PATH--->%JAVA_HOME%\lib
但是这样是有问题的，比如你有这样的程序：
```
package org.net;

/**
 * Created by V-zar on 2017/5/22.
 */
public class Test {

    public static void main(String[] args) {


        System.out.print("HelloWorld");

        }

    }

```

当你在某个目录编译`javac Test.java`后，试图用java命令`java Test`运行的时候,会发现报错`找不到或无法加载主类`。这是因为按照很多资料那样来配置的时候CLASS_PATH是错误的，解决的办法有两个：
+ 运行时加入参数`java -cp . Test` (运行时指定classpath为当前路径)。
+ 在环境变量CLASS_PATH中加入`.`,表示当前路径，Java解释器将在当前路径搜索class
  文件，如果CLASS_PATH中有多个路径值，建议将`.`置顶。（一劳永逸的方法）。

另外，如果你是用Eclipse或者IDEA编辑程序的话，一般会在程序开头加入包的信息，如
`package org.net;`,这同样会导致错误，所以如果是用Eclipse或者IDEA编辑程序并且试图在命令行运行程序的话，最好检查一下程序开头有没有包的信息，有的话需要删掉:-).


