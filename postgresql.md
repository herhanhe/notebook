---
title: 
tags: 
notebook: 
---
# `Oracle` 数据库向 `Postgresql`迁移心得

## `Oracle`与 `Postgresql` 使用 差异对比

### JDBC连接：
```sql
    db.url=jdbc:oracle:thin:@localhost:1521:ORCL
    db.url=jdbc:postgresql:@localhost:5432/database
```
### 1、基本数据类型差异
`Oracle`|`Postgresql`
-|-
`varchar2`|`varchar`
`number`|`numeric`
`date`|`timestamp/date/time`
不支持`boolean`,可通过0/1代替|支持`boolean`

### 2、基本函数差异
`item`|`Oracle`|`PostgreSQL`
-|-|-
系统当前时间|`SYSDATE`|`now()/CURRENT_TIMESTAMP/CURRENT_DATE/CURRENT_TIME`
对时间或数字截取|`trunc()`|`date_trunc()`
判空操作|`nvl()`|`coalesce()`
`dual`伪表|支持|不支持（查询常量不需要加`from`）
