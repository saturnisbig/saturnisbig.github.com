---
layout: post
title: "sqlite export and import"
description: ""
categories: [programming]
tags: [sqlite, python]
---
{% include JB/setup %}


## export

-to sql file

        $sqlite test.db
        sqlite>.output data.sql
        sqlite>.dump (table1 table2)

-to another db


        $sqlite3 test.db ".dump" | sqlite3 test2.db

-export table structure


        $sqlite3 db1
        $sqlite> .tables
        t1  tx
        $sqlite> select * from t1;
        |A
        |B
        $sqlite> .q
        
        $sqlite3 db2
        $sqlite> .tables
        $sqlite> ATTACH DATABASE db1 as db1;
        $sqlite> select * from db1.t1;
        |A
        |B
        $sqlite> create table t2 as select * from db1.t1 where 1=2;
        $sqlite> select * from t2;
        |A
        |B
    
## import


    sqlite>.seperator ","
    sqlite>.import data.sql db_table
    sqlite>select * from db_table


## 使用工具

用SQLite Developer,工具->提取数据库SCHEMA和数据-> 选择表->输入导出文件名，既可导出 


## 和MySQL的转换

1. mysql2sqlite

convert mysql to sqlite.

    mysqldump -h 127.0.0.1 -P 3307 -u root --default-character-set=utf8 -ppasswd dbname | dumpfile.txt
    $print hello 
    
2. sqlite to mysql


