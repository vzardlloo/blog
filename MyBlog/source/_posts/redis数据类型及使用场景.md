---
title: redis数据类型及使用场景
date: 2020-09-08 00:46:44
tags: redis
categories: redis
---

## Redis的数据类型

#### 数据类型对应编码方式

| 数据类型 |               编码方式               |
| :------: | :----------------------------------: |
|  String  |        `raw`、`embstr`、`int`        |
|   Hash   |        `hashtable`、`ziplist`        |
|   List   | `linkedlist`、`ziplist`、`quicklist` |
|   Set    |        `hashtable`、`intset`         |
|   Zset   |        `skiplist`、`ziplist`         |

#### 编码类型对应数据结构

|   编码方式   |         数据结构         |
| :----------: | :----------------------: |
|    `raw`     |      动态字符串编码      |
|   `embstr`   | 优化内存分配饿字符串分配 |
|    `int`     |         整数编码         |
| `hashtable`  |        散列表编码        |
|  `ziplist`   |       压缩列表编码       |
| `linkedlist` |       双向链表编码       |
| `quicklist`  |       快速列表编码       |
|   `intset`   |       整数集合编码       |
|  `skiplist`  |        跳跃表编码        |

## 使用场景

| 数据类型 |                           使用场景                           |
| :------: | :----------------------------------------------------------: |
|  String  |                    普通的k-v缓存,分布式锁                    |
|   Hash   | 字典结构，比如缓存一个创意的消耗信息,创意id对应曝光数、点击数等多个subkey |
|   List   |                     关注列表,简单的队列                      |
|   Set    |                       去重器，集合运算                       |
|   Zset   |                      排行榜,优先级队列                       |











