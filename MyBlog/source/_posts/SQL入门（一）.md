---
title: SQL入门（一）
date: 2017-03-20 22:33:27
tags:
categories: SQL
---
  这是我记录和梳理自己学习SQL的系列文章的第一篇。这系列文章主要以MySQL为学习对象，但其他数据库也基本适用。
  首先我们要明确几个最近的概念：
  + 数据库：保持表和其他相关SQL结构的容器。
  + 表：数据库中包含数据的结构，由行和列组成。
  + 列：存储表中的一块数据。
  + 行：一组能够描述某个事物的列的集合。

  下面是基本语法：
  1. 显示数据库：`SHOW DATABASES;`
  2. 指明使用某个数据库：`USE database_name;`
  3. 显示数据库中的表：`SHOW TABLES;`
  4. 创建一个数据库：`CREATE DATABASE database_name;`
  5. 创建一个数据表：
            ```
            CREATE TABALE table_name(
                    [column_name_1]  datatype_1,
                    [column_name_2]  datatype_2,
                    ...
                    [column_name_n]  datatype_n
                );

            ```
  
  下一篇介绍数据库的基本数据类型和基本的增删改查。