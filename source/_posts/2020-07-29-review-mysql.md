---
title: 复习 Mysql
thumbnail: 'https://cdn.sguan.top/markdown/20200827133345.png'
disqusId: 312a9353-af74-455f-9d75-ead442e3080c
categories:
  - ["database","mysql"]
tags:
  - mysql
  - mysql 8.0
date: 2020-07-29 19:10:53
---

这不是一个完整的教程，知识我重新学习 Mysql 的知识，并做了一些零碎笔记。

<!-- more -->

## 别名

对于字段别名可以这样：

```mysql
select name as 姓名,age from tb_user;
```

也可以：

```mysql
select name 姓名,age from tb_user;
```

> 多个字段用 `,` 分隔，字段别名可以不加 as，直接用空格分隔即可。

表别名类似，可以这样：

```mysql
select A.name from tb_user as A;
```

也可以：

```mysql
select A.name from tb_user A;
```

## DISTINCT

distinct 是对结果去重的，它是作用在所有选择字段上的，而不是单个字段。

例如：

```mysql
select distinct name,age from tb_user;
```

其效果类似于：

```mysql
select distinct (name,age) from tb_user;
```

如果我们想要对查询多个字段，且根据某一个字段去重，应该使用 `GROUP BY`：

```mysql
select name,max(age) from tb_user group by name;
```

## BETWEEN AND 边界

下面的语句类似于，age >= 20 && age <= 30

```mysql
select * from tb_user where age BETWEEN 20 AND 30; 
```

下面的语句类似于，age < 20 || age > 30

```mysql
select * from tb_user where age NOT BETWEEN 20 AND 30; 
```

## ESCAPE

在使用 like 子句的通配符时，如果字段恰好包含了通配符怎么办呢？

例如：在下面这个查询中，我们确实想查询包含 `前5%` 关键字的内容，默认情况下 `%` 会被视为通配符。

```mysql
select * from where tb_user where description like "%前5%%"; 
```

此时我们可以使用 ESCAPE，escape 意为避开、避免，在 like 子句中，escape 字符后面的通配符会被视为普通字符：

```mysql
select * from where tb_user where description like "%前5!%%" escape '!'; 
select * from where tb_user where description like "%前5#%%" escape '#'; 
```

## 参考链接

[name](https://github.com/3ks)