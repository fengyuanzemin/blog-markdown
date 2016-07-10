---
title: SQL中Inner Join和Outer Join的区别
date: 2016-03-10 08:30:19 
tags: SQL
---

假设有两个表a和b，现在要对它俩进行连接查询

Table A

|aid| a_data|
|---|---| 
|1   |    a1 |
|2   |   a2 |
|3    |  a3|
 
Table B
 
|bid| b_data|
|---|---| 
|1|    b1 |
|2|   b2| 
|4|    b4| 

<!--more-->


# Inner Join

```sql
select * from a, b where a.aid = b.bid;
select * from a inner join b on a.aid = b.bid;
```

这是仅取出匹配的数据. 

此时的取出的是: 

|id|a_data|b_data|
|---|---|---|
|1| a1| b1| 
|2| a2| b2| 

# Outer Join 
## Left Join

```sql
select * from a left join b on a.aid = b.bid;
```
首先取出a表中所有数据，然后再加上a、b匹配的数据 

此时的取出的是: 

|id|a_data|b_data|
|---|---|---|
|1| a1| b1| 
|2| a2| b2| 
|3| a3| 空字符| 
 
## Right Join 


```sql
select * from a right join b on a.aid = b.bid;
```

指的是首先取出b表中所有数据，然后再加上a、b匹配的数据 

此时的取出的是: 

|id|a_data|b_data|
|---|---|---|
|1| a1| b1| 
|2| a2| b2| 
|4| 空字符 |b4|
 

## Full Join

取出a、b表中的所有数据，不管是否存在匹配行

```sql
select * from a full join b on a.aid = b.bid;
```

|id|a_data|b_data|
|---|---|---|
|1|a1|b1|
|2|a2|b2|
|3|a3|空字符|
|4|空字符|b4|


# Cross Join

笛卡尔乘积（所有可能的行对），交叉连接用于对两个源表进行纯关系代数的乘运算。它不使用连接条件来限制结果集合，而是将分别来自两个数据源中的行以所有可能的方式进行组合。

```sql
select * from a cross join b;
```