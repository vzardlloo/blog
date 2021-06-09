---
title: JAVA基础回顾-双亲委派模型
date: 2017-08-15 10:49:02
categories: JAVA
tags: jvm
---
#### 前言
![](http://oo3aq3ac8.bkt.clouddn.com/mother.jpg)
"give it to your mother,she knows how to do it",这是一张温馨又搞笑的图片，温馨在于展示了母亲对我们的关爱与呵护，搞笑是表现了很多人对衣物上的那些各种标识的含义一无所知的现状。但是我在看到这张图的时候想到的却不是这个，我想的是如果一个人遵从指示"give it to his/her mother",然后mother又遵从指示"give it to her mother"(也就是给了奶奶)，如果奶奶又遵从指示"give it to her mother"...想到这我不禁感叹幸亏人类的寿命是有限的，不然这件衣服永远也洗不成:-),但是类似这样的事情在JAVA的世界里是真实存在的，那就是双亲委派模型。
<!--more-->
#### 类加载器
在JAVA中对于任意一个类，都需要由加载他的类加载器和这个类本身来一同确定其在虚拟机中的唯一性，每一个类加载器都有一个独立的类名称空间。也就是说比较两个是否“相等”,只有在这个两个类是由同一个类加载器加载的前提下才有比较的意义，否则，就算这两个来源自同一个Class文件，被同一个虚拟机加载，只要不是被同一个类加载器加载，那这两个类就必然不相等。下面用代码举例说明：
```java
package myutil;

import java.io.IOException;
import java.io.InputStream;

/**
 * Created by V-zar on 2017/8/15.
 */
public class ClassLoaderTest {

    public static void main(String[] args) throws Exception{

        ClassLoader myClassLoader = new ClassLoader() {
            @Override
            public Class<?> loadClass(String name) throws ClassNotFoundException {
               try{
                   String fileName = name.substring(name.lastIndexOf(".")+1)+".class";
                   InputStream is = getClass().getResourceAsStream(fileName);
                   if(is == null){
                       return super.loadClass(name);
                   }
                   byte[] b = new byte[is.available()];
                   is.read(b);
                   return defineClass(name,b,0,b.length);
               }catch (IOException e){
                    throw new ClassNotFoundException(name);
               }


            }
        };

        Object obj = myClassLoader.loadClass("myutil.ClassLoaderTest").newInstance();
        System.out.println(obj.getClass());         //#1
        System.out.println(obj instanceof myutil.ClassLoaderTest);  //#2
        System.out.println(myutil.ClassLoaderTest.class.getClassLoader());//#3
        System.out.println(obj.getClass().getClassLoader());    //#4
    }



}
```
运行结果：
```
class myutil.ClassLoaderTest
false
sun.misc.Launcher$AppClassLoader@18b4aac2
myutil.ClassLoaderTest$1@6e0be858
```
在代码中我们构造了一个简单的类加载器，它可以加载与自己在同一路径下的Class文件。我们用这个类加载器来加载`myutil.ClassLoaderTest`这个类，并且实例化了这个类的对象obj。在代码#1处我们获取obj的Class对象，发现权限定名为`myutil.ClassLoaderTest`。下一步代码#2是将obj与权限定名为`myutil.ClassLoader`的类作instanceof比较，但是结果为false,这个结果看似很不合常理，因为在#1处已经获取obj的Class对象，权限定名就是`myutil.ClassLoaderTest`。接着看#3和#4,我们分别获取了obj和`myutil.ClassLoader`的类加载器并将其打印到控制台,从打印结果可以看出两者的加载器并不相同。这就是#2处程序执行结果为false的原因。
#### 双亲委派模型
从上面的例子可以看出类加载器对于Java类的重要性，但是对于众多的类加载器必然需要用一种方式来组织它们不然势必会造成混乱。在Java中类加载器的组织模型就是双亲委派模型。双亲委派模型的工作过程是：如果一个类加载器收到了类加载请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成("give it to your mother:D"),然后每一层都是这样，因此所有的类加载器最终都应该传送到最顶层的类加载器中，当上层类加载器无法完成这个加载请求时(他的搜索范围中没有找到所需要的类)，子加载器才会尝试自己去加载。这样有个显而易见的好处就是Java类随着它的类加载器一起具备了一种层级关系，可以让最基础的、使用范围最广的一些类由最顶层的类加载器来加载，可以保证在任何类加载器环境下它都可以被正确的加载。比如java.lang.Object，如果不用双亲委派模型来加载的话，如果用户自行编写了一个称为java.lang.Object，并且放置在ClassPath中， 那系统中将会出现多个不同的Object类，那么JAVA类型体系中最基础的行为也就得不到保证。
在JAVA中双亲委派模型的实现代码主要在java.lang.ClassLoader的loadClass()方法中，源码如下：
```java
protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // First, check if the class has already been loaded
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }

```
分析：逻辑很简单粗暴，先检查有没有被加载过，如果没有则调用父类的loadClass()方法，若父加载器为空则默认使用JAVA中的顶级类加载器--Bootstrap ClassLoader作为父类加载器来尝试加载，如果父类加载失败则抛出ClassNotFoundException异常后，再调用自己的findClass。最后，这些过程都是同步的。
Java 中的类加载器大致可以分成两类，一类是系统提供的，另外一类则是由 Java 应用开发人员编写的。系统提供的类加载器主要有下面三个：
+ 引导类加载器（bootstrap class loader）：它用来加载 Java 的核心库，是用原生代码来实现的，并不继承自 java.lang.ClassLoader。
+ 扩展类加载器（extensions class loader）：它用来加载 Java 的扩展库。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。
+ 系统类加载器（system class loader）：它根据 Java
应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java
应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它。
除了系统提供的类加载器以外，开发人员可以通过继承java.lang.ClassLoader类的方式实现自己的类加载器，以满足一些特殊的需求。
>类加载器树状组织结构示意图
![](http://oo3aq3ac8.bkt.clouddn.com/classloader.png)

另外值得一提的是JAVA的双亲委派模型不是一个强制性的约束模型，只是一种JAVA设计者推荐的类加载器实现方式，在有些时候还需要打破这种模式（例如利用Thread ConText ClassLoader 加载 SPI代码）和OSGi技术。


