---
layout: post
title: "When to use FMDatabaseQueue"
description: ""
categories:[ios-dev]
tags: [iOS-dev, FMDatabaseQueue]
---
{% include JB/setup %}

2014-06-19 23:21:13

昨天在使用FMDB操作sqlite进行查询，插入等操作的时候，碰到了
'Database is locked when updateing'的问题，我的目的是这样的：

数据库里面有两张表t1、t2，在将数据插入t1后，我需要对t2的一些
数据进行查询、更新操作。原来对sqlite的操作，我都是通过初始化
一个`FMDatabase`实例来操作，但是要同时进行多项操作，就一直提示
'Database is locked'等。

对错误的判断：
自己对错误提醒的理解是，应该是FMDatabase不支持同时进行多个操作，
导致错误，那么有没有什么解决的办法呢？

解决办法：
FMDB项目的说明文档里面提到了，对一些需要线程安全的操作，最好
是使用FMDatabaseQueue，文档里面有个实例的代码是这样的：

    // 首先创建一个实例
    FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:aPath];
    // 通过block执行所需操作
    [queue inDatabase:^(FMDatabase *db) {
        [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:1]];
        [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:2]];
        [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:3]];

        FMResultSet *rs = [db executeQuery:@"select * from foo"];
        while ([rs next]) {
            …
        }
    }];


这样解决了问题。对Objective-C的block语法及如何使用，自己并不是很了解，
明天把这块给补上。

2014-06-19 23:32:00 
