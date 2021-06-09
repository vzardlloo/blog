---
title: SQl学习（二）
date: 2017-03-22 20:28:27
tags:
categories: SQL
---
这篇介绍数据库的基本数据类型和基本的增删改查：

基本数据类型：（MySQL）

1. 整数类型：
    + `TINYINT` : 占用1个字节
    + `SMALLINT`　：占用2个字节
    + `MEDIUMINT` : 占用3个字节
    + `INT` : 占用4个字节
    + `BIGINT` :　占用８个字节

2. 浮点类型：
    + `FLOAT` : 占用4个字节
    + `DOUBLE` : 占用8个字节
    + `DECIMAL(M,D)` :　M+2

3. 日期与时间:
    + `YEAR` : 1901~2155    YYYY
    + `DATE` : 1000-01-01~9999—12-3     YYYY-MM-DD
    + `TIME` : -838:59:59~838:59:59     HH:MM:SS
    + `DATETIME` : 1000-01-01 00:00:00~9999-12-31 23:59:59  YYYY-MM-DD HH:MM:SS
    + `TIMESTAMP` :　1970-01-01 00:00:01~2038-01-19 03:14:07     YYYY-MM-DD 

4. 字符串&二进制:
    + `CHAR` : 表示长度固定的字符串
    + `VARCHAR` :　表示可变长度的字符串
    + `BINARY` : 表示固定长度的二进制数据
    + `VARBINARY` :　表示可变长度的二进制数据
    +  `BLOB` :　表示二进制大数据
    +  `TEXT` :　用于表示大文本数据
    +  `ENUM` :　用于枚举类型，只能存储一个枚举类型的值
    +  `SET` : 表示字符串对象
    +  `BIT` : 表示位字段类型



基本增删改查：

1. INSERT（增）：
    + 插入一条记录：

        ```
        INSERT INTO table_name ([column_1],[column_2],...[column_n])
        VALUES(values_1,values_2,...,values_n);
        ```


        需要注意[column_x] 与 values_x 的对应关系。可以省略`[column_x]`列表，
        但是此时必须将`values_x`写全并与表中的列对应。也可以省略部分，省略的列将由`NULL`填充。

2. DELETE(删):
    + 删除表中全部数据：`DELETE FROM table_name`。
    + 删除表中指定数据：
            ```
            DELETE FROM table_name WHERE "XXX"="xxx" [AND] "YYY" = "yyy" [OR]
            "ZZZ" = "zzz";
            ```
3. UPDATE(改)：
    + 改变表中指定记录：
          ```
          UPDATE FROM table_name SET　"XXX" = "xxx"　WHERE "XXX" = "yyy" ;
          ```
    + 同时改变多条记录：
          ```
          UPDATE FROM table_name SET [column_1] = 'new_1' , 
                                     [column_2] = 'new_2' ,
                                     ...
                                     [column_n] = 'new_3' ;
                         WHERE [column_1] = 'old_1' AND 'old_2'...AND 'old_n';
          ```
4. SELECT(查)：
   + 查看表中所有数据：`SELECT * FROM　table_name;`
   + 查看表中指定数据：
          ```
          SELECT [column_1],[column_2]...[column_x] FROM table_name 
          WHERE
            [column_y] = "something" [AND] ... [OR] ...[BETWEEN]...
         ```