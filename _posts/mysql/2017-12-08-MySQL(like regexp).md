---
layout: blog
istop: true
category: MySQL
title:  MySQL语句（LIKE REGEXP）
date:   2017-12-5 10:06:42
background-image: https://i.loli.net/2017/12/08/5a2a5b9fde563.png

tags:
- MD
- MySQL
---
# <center>LIKE通配符（通过部分已知量查询匹配）
***
> %：匹配任意长度任意值。\
> _ ：匹配单个任意值。
***
> 进行LIKE 前后%，_查询：`SELECT NAME FROM al_winner WHERE NAME LIKE '%Aaro_'`
![MySQL](https://i.loli.net/2017/12/08/5a2a5b9fde563.png)
***


# <center>REGEXP正则表达式查询
> 进行REGEXP OR查询 `SELECT NAME FROM al_winner WHERE NAME REGEXP 'SELE|MOYER'`

![MySQL](https://i.loli.net/2017/12/08/5a2a5bc19283f.png)


>TIP正则表达式中任意数匹配与LIKE不一样
a.尽量不要使用通配符查询，
b.若必须使用，也尽量不要在开始就使用通配符查询