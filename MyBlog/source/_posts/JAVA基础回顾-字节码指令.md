---
title: JAVA基础回顾-字节码指令
date: 2017-08-10 16:34:19
categories: JAVA
tags: jvm
---
#### 引言
众所周知，Java程序是运行在Java虚拟机上的，而这里的“虚拟”是对什么东西进行虚拟呢？答案当然就是对“实体”机进行虚拟啦，虚拟机可以看做是对实体机进行了进一步的封装和抽象，隐藏了不同实体机之间的差别，从而达成“Write Once,Run AnyWhere”的目标。既然虚拟机是对实体机的虚拟，所以我认为虚拟机和实体机在结构和功能上必然存在某种程度上的对应与关联。因此我们在学习时应该注意发掘和类比两者之间的关系。
本着这样的思想，我们进行Java字节码指令的学习。JAVA字节码在JAVA虚拟机中的地位相当于实体机的机器码，一切在Java虚拟机上运行的程序都要被解释或编译成字节码，一切在实体机上运行的程序最后也都要编译成机器码。Java字节码指令可以对字节码进行操作，在实体机中对机器码进行操作的是汇编语言。所以Java字节码指令对应汇编语言，Java字节码指令集对应汇编指令集。

#### 字节码简介
Java字节码指令由*一个*字节长度的，代表某种特定操作含义的数字（操作码）以及其后的零至多个代表此操作所需参数（操作数）。此外字节码指令是面向操作数栈的，这里操作数栈在功能上对应实体机的寄存器但是结构上有所区别。
<!--more-->
#### 字节码与数据类型
在字节码指令集中，大多数指令都对应的其操作所对应的数据类型信息，比如iload表示从局部变量表中加载int型的数据到操作栈中，fload从局部变量表中加载float型的数据到操作栈中...但是由于Java字节码的操作码只有一个字节（即0~255），这意味着指令集的操作码总数不可能超过256条。所以如果要求Java运行时所有的数据类型都有对应的与数据类型相关的指令去支持的话，操作码的总数将超过256条。所以JAVA字节码指令集被设计为Not Orthogonal（非完全独立）,即并非每种数据类型和每种操作都有对应的指令，有一些指令可以在必要的时候将一些不被支持的数据类型转换为被支持的数据类型。我们可以以数据类型为列，操作指令为行制作一张表，其中为空的项即说明虚拟机不支持对这种数据类型进行这项操作。
![](http://oo3aq3ac8.bkt.clouddn.com/jvmcode.PNG)

#### 加载和存储指令
加载和存储指令用于将数据在帧栈中的局部变量表和操作数栈之间传输。
+ 将一个局部变量表加载到操作数栈：
iload、`iload_<n>`、lload、lload_<n>、fload、fload_<n>、dload、`dload_<n>`、aload、`aload_<n>`。
+ 将一个数值从操作数栈储存到局部变量表：
istore,`istore_<n>`,lstore,`lstore_<n>`,fstore,`fstore_<n>`,dstore,`dstore_<n>`,astore,`astore_<n>`。
+ 将一个常量加载到操作数栈：
```
bipush,sipush,lde,lde_w,ldc2_w,aconst_null,iconst_ml,iconst_<i>,lconst_<i>,fconst_<i>,dconst_<i>。
```
+ 拓充局部变量表的访问引索的指令：wide

#### 运算指令
运算指令用于对操作数栈上的值进行某种特定的运算。
+ 加法运算：iadd,ladd,fadd,dadd。
+ 减法运算：isub,lsub,fsub,dsub。
+ 乘法运算：imul,lmul,fmul,dmul。
+ 除法运算：idiv,ldiv,fdiv,ddiv。
+ 求余指令：irem,lrem,frem,drem。
+ 取反指令：imeg,lmeg,fmeg,dmeg。
+ 位移指令：ishl,ishr,iushr,lshl,lshr,lushr。
+ 按位或指令：ior,lor。
+ 按位与指令：iand,land。
+ 按位异或指令：ixor,lxor。
+ 局部变量自增指令：iinc。
+ 比较指令：dcmpg,dcmpl,fcmpg,fcmpl,lcmp。
*注：只有在除法指令（idiv,ldiv)和求余指令（irem,lrem)当出现除数为零时会导致虚拟机抛出AirtmeticException异常，其余整形和浮点型运算场景都不会抛出异常*

#### 类型转换指令
类型转换指令可以将两种不同数值类型进行相互转换。
Java虚拟机天然支持基本数据类型的宽化类型转换，例如int到long、flost、double等。
对于窄化数据类型转化则必须用显示的转换指令：
+ i2b(int -> boolean)
+ i2c(int -> char)
+ i2s(int -> short)
+ l2i(long -> int)
+ f2i(float -> int)
+ f2l(float -> long)
+ d2i(double -> int)
+ d2l(double -> long)
+ d2f(double -> float)
*几点说明：*
+ int/long 类型窄化转换为整数类型T时，转换过程为丢弃除最低位N（T的数据类型长度）个字节以外的内容。
+ 浮点值窄化转换为整数类型T（int/long)时：
```
if(浮点值==NaN){
    result = 0;
}else{
    value = [浮点值];  //向下取整
    if(T.min <= value <= T.max){    //value在T的表示范围内
        result = value;
    }else{
        if(value > 0) result = T.max;
        if(value < 0) result = T.min;
    }
}

```

#### 对象创建与访问指令
+ 创建类实例的指令：new
+ 创建数组的指令：newarray,anewarray,multianewarray
+ 访问类字段（static字段）和实例字段（非static字段）的指令：getfield,putfield,getstatic,putstatic
+ 将一个数组元素加载到操作数栈的指令:
baload,caload,saload,iaload,faload,daload,aaload
+ 将一个操作数栈的值存储到数组元素中的指令：
bastore,castore,iastore,sastore,fastore,fastore,dastore,aastore
+ 取数组长度的指令：arraylength
+ 检查类实例类型的指令：instanceof,checkcast

#### 操作数栈管理指令
+ 将一个操作数栈的栈顶一个或两个元素出栈：pop、pop2。
+ 复制栈顶一个或两个数值并将复制值或双份的复制值重新压入栈顶：dup、dup2、dup_x1,dup2_x1,dup_x2,dup2_x2。
+ 将栈顶端的两个数值交换：swap。

#### 控制转移指令
控制转移指令可以让Java虚拟机有条件或者无条件的从指定的位置而不是控制转移指令的下一条指令继续执行程序。
+ 条件分支：
ifeq,ifit,ifle,ifgt,ifnull,ifnonnull,if_icmpeq,if_icmpne,if_icmplt,if_icmpgt,if_icmple,if_icmpge,if_acmpeq,if_acmpne。
+ 复合条件分支：tableswitch,lookupswitch。
+ 无条件分支：gosto,goto_w,jsr,jsr_w,ret。

#### 方法调用和返回指令
+ invokevirtual:用于调用对象的实例方法，根据对象的实际类型进行分派（虚方法分派）。
+ invokeinterface:用于调用接口方法，它在运行时搜索一个实现了这个接口方法的对象，找出适合的方法进行调用。
+ invokespecial:用于调用一些需要特殊处理的实例方法，包括实例的初始化方法，私有方法和父类方法。
+ invokestatic:用于调用类方法（static方法）
+ invokedynamic:用于运行时动态解析出调用点限定符所应用的方法，并执行该方法。（前面的分派逻辑都固化在虚拟机内部，而该指令的分派逻辑是由用户自定义）。
+ 方法返回指令：ireture(返回类型是int,short,byte,char,boolean时),lreturn,freturn,dreturn,areturn,另外还有一条return供void方法、实例/类/接口的初始化方法使用。

#### 异常处理指令
显式抛出异常指令：athrow

#### 同步指令
+ monitorenter,monitorexit




#### 小练习
我们拿Java里面比较经典的i++和++i问题来做个练习，熟悉下用字节码分析问题：
Test Case for ++i:
```java
public class Test_1 {
    public static void main( String[] argv )
    {
        int  value = 0;
        value = ++value;

        System.out.println(value);
    }
}
```

运行结果：1。
对应部分字节码及分析：
```
Code:
       0: iconst_0                 //将0加载到栈顶
       1: istore_1                 //将0存储到变量value
       2: iinc          1, 1       //value在局部变量表自增为1,(此处为虚指令，真实的变量操作要靠load和store指令)
       5: iload_1                  //将value的值加载到栈顶
       6: istore_1                 //将栈顶的内容保存到变量value，value=1
       7: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: iload_1
      11: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
      14: return
```

Test Case for i++:
```java
public class Test {


    public static void main( String[] argv )
    {
       int  value = 0;
       value = value++;

        System.out.println(value);
    }


}

```

运行结果：0
字节码及分析：
```
 Code:
       0: iconst_0              //将0加载到栈顶
       1: istore_1              //将0存储到变量value
       2: iload_1               //将value的值加载到栈顶,栈顶为0
       3: iinc          1, 1    //value在局部变量表自增为1
       6: istore_1              //将栈顶的内容保存到变量value,value=0
       7: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: iload_1
      11: invokevirtual #3                  // Method java/io/PrintStream.println:(I)V
      14: return
```

以上通过字节码分析对这个问题无疑有了更深层次的理解。