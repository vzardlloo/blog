---
title: JAVA基础回顾-自定义注解
date: 2017-07-11 20:18:00
tags: JAVA
categories: JAVA
---
这篇博客我们来回顾一下Java基础中的注解，主要通过来自定义一套注解来回顾一下这块的知识。
>下面先回顾一下基础知识：

四个元注解：
+ @Target
+ @Retention
+ @Documented
+ @Inherited
  

*具体说明: *   
+ @Target
@Target说明了Annotation所修饰的对象范围：Annotation可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）。在Annotation类型的声明中使用了target可更加明晰其修饰的目标。

> 作用：用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

  取值(ElementType)有：
　　1. CONSTRUCTOR:用于描述构造器
　　2. FIELD:用于描述域
　　3. LOCAL_VARIABLE:用于描述局部变量
　　4. METHOD:用于描述方法
　　5. PACKAGE:用于描述包
　　6. PARAMETER:用于描述参数
　　7. TYPE:用于描述类、接口(包括注解类型) 或enum声明
<!--more-->　　　　

+ @Retention
@Retention定义了该Annotation被保留的时间长短：某些Annotation仅出现在源代码中，而被编译器丢弃；而另一些却被编译在class文件中；编译在class文件中的Annotation可能会被虚拟机忽略，而另一些在class被装载时将被读取（请注意并不影响class的执行，因为Annotation与class在使用上是被分离的）。使用这个meta-Annotation可以对 Annotation的“生命周期”限制。

> 作用：表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）

取值（RetentionPoicy）有：
　　1. SOURCE:在源文件中有效（即源文件保留）
　　2. CLASS:在class文件中有效（即class保留）
　　3. RUNTIME:在运行时有效（即运行时保留）

+ @Docmented
@Documented用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。Documented是一个标记注解，没有成员。

+ Inherited
@Inherited 元注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。
　　注意：@Inherited annotation类型是被标注过的class的子类所继承。类并不从它所实现的接口继承annotation，方法并不从它所重载的方法继承annotation。
　　当@Inherited annotation类型标注的annotation的Retention是RetentionPolicy.RUNTIME，则反射API增强了这种继承性。如果我们使用java.lang.reflect去查询一个@Inherited annotation类型的annotation时，反射代码检查将展开工作：检查class和其父类，直到发现指定的annotation类型被发现，或者到达类继承结构的顶层。

>自定义一套注解：

我们的目的是定义一套注解，可以通过注解来配置人的个人信息，包括姓名，性别，年龄。
+ 首先定义@Name，@Gender,@Age三个标签:

```java
package annotation_learn;

import java.lang.annotation.*;

/**
 * 定义@Name注解
 * Created by V-zar on 2017/7/11.
 */


@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Name {
    String value() default "";
}

```

```java
package annotation_learn;

import java.lang.annotation.*;

/**
 * 定义@Gender注解
 * Created by V-zar on 2017/7/11.
 */


@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface Gender {

    public enum Sex{MALE,FEMALE};

    Sex value() default Sex.MALE;
}

```

```java
package annotation_learn;

import java.lang.annotation.*;

/**
 * 定义@Age注解
 * Created by V-zar on 2017/7/11.
 */
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented

public @interface Age {

    int value() default 0;
}

```

+ 其次定义一个注解处理类，不然注解不会比注释更有用:-)

```java
package annotation_learn;

import java.lang.reflect.Field;

/**
 * 定义注解处理类
 * Created by V-zar on 2017/7/11.
 */
public class PersonInfoUtil {
    public static void getPersonInfo(Class<?> clazz){
        String strPersonName = "姓名：";
        String strPersonSex = "性别：";
        String strPersonAge = "年龄：";

        Field[] fileds = clazz.getDeclaredFields();

        for (Field filed : fileds){
            if(filed.isAnnotationPresent(Name.class)){
                Name name = (Name) filed.getAnnotation(Name.class);
                System.out.println(strPersonName+name.value()+"\n");
            }else if(filed.isAnnotationPresent(Gender.class)){
                Gender sex = (Gender) filed.getAnnotation(Gender.class);
                System.out.println(strPersonSex+sex.value()+"\n");

            }else if(filed.isAnnotationPresent(Age.class)){
                Age age = (Age) filed.getAnnotation(Age.class);
                System.out.println(strPersonAge+age.value()+"\n");
            }


        }



    }


}

```

+ 定义一个人物类来测试注解：

```java
package annotation_learn;

/**
 * 定义人物类
 * Created by V-zar on 2017/7/11.
 */
public class Person {

    @Name("Jack")
    private String Name;
    @Gender(Gender.Sex.MALE)
    private String Sex;
    @Age(23)
    private int Age;
}

```

+ 运行测试主函数：

```java
package annotation_learn;

/**
 * 测试用主函数
 * Created by V-zar on 2017/7/11.
 */
public class Runner {
    public static void main(String[] args) {
        PersonInfoUtil.getPersonInfo(Person.class);
    }
}

```

+ 运行结果：

```
姓名：Jack

性别：MALE

年龄：23


Process finished with exit code 0
```

>自定义注解成功！