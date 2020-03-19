---
title: MySQL索引笔记
date: 2020-03-17
tags: MySQL
---

提到索引，就会看到诸如聚簇索引、非聚簇索引、主键索引、唯一索引、普通索引、哈希索引、全文索引、B树索引......经常搞混，特此整理一篇索引笔记以供查阅。

<!--more-->

## 索引的数据结构

**二叉树**

基于平衡二叉树的查找相当于二分查找，数据的比较次数较少，但是数据量较大时，树的高度较高，查询时会产生大量的磁盘`IO`，效率极差。如下图，每一个节点就会进行一次磁盘`IO`，与查询的数据进行比对后决定查询方向。

另一方面，可能会随着数据的变化退化为单链表。

**红黑树**

自动进行平衡的二叉树，防止退化为单链表。如上例数据进行删减后，红黑树会自动平衡生成新的平衡二叉树。

**B树**

B树是一颗多路平衡树，每一个节点存储多个关键字以及对应的数据，并存储指向下一层节点的指针。因为节点还存有实际数据的原因，因此每个节点存储的关键字比B+树少，数据量较大时树的高度会略高。

**B+树**

在B树的基础上扩展，非叶子节点只存储关键字，因此每个节点能存储更多的关键字，所以树的高度较低，磁盘`IO`次数较少。并且每个节点还存有相邻节点的指针，有利于进行范围查询。实际数据只存储在叶子节点，因此对于每一次查询都需要所搜到最后，但是由于树的高度较低，所以平均效率较好。

**哈希**

通过哈希算法计算数据的哈希值，得到数据的存放位置，当发生哈希碰撞时在哈希值所在位置创建一个链表来存放相同哈希值的数据。

在数据量很大的情况下内存无法加载全部的数据索引。

## 聚簇索引与非聚簇索引

**聚簇索引**

聚簇索引指的是节点中存储着实际数据的索引，查找到了索引也就找到了数据。一个表只有一个聚簇索引，通常为主键索引，如果没有为表指定主键，实际上系统会隐式创建一个聚簇索引。

可以理解为主索引，每一次查询实际最后查到数据都是利用这个主索引。

**非聚簇索引**

非聚簇索引指的是节点存储的是真实数据的主键，需要利用查询到的主键值再到主索引进行一次查询。例如复合索引、唯一索引、前缀索引。

## MySQL的索引类型

**主键索引**

一个表最多只有一个，值唯一。

```mysql
create table test(
  id int not null auto_increment,
	name varchar(10)
	primary key(id)
);
alter table test add constraint pk primary key(id);
#对于字符串字段还可以指定索引长度 name(10)
```

**普通索引**

```mysql
create table test(
	name varchar(10)
	index idx_name(name)
);
create index idx_name on test(name);
alter table test add index idx_name(name);
#对于字符串字段还可以指定索引长度 name(10)
```

**唯一索引**

索引列的值必须唯一，但可以为空值。

```mysql
create table test(
	name varchar(10)
	unique idx_unique_name(name)
);
create unique index idx_unique_name on test(name);
alter table test add unique index idx_unique_name(name);
#对于字符串字段还可以指定索引长度 name(10)
```

**联合索引 / 组合索引 / 复合索引**

指定多个字段创建索引。如（a, b, c），则在查询条件中（a）、（a, b）、（a, c）、（a, b, c）这几种`and`组合时，索引才会生效。

```mysql
create table test(
	name varchar(10),
  age tinyint,
	index idx_name_age(name, age)
);
create index idx_name_age on test(name, age);
alter table test add index idx_name_age(name, age);
#对于字符串字段还可以指定索引长度 name(10)
```

**全文索引**

主要用来查找文本中的关键字，少量的数据可以使用`like`，但当文本数据较大时这种方式效率是很低的。

```mysql
create table test(
	id int not null auto_increment,
  title varchar(100) not null default "",
  content text null,
  primary key(id),
  fulltext(contents)
);
create fulltext index idx_fulltext_content on test(content);
alter table test add fulltext index idx_fulltext_content(content);
```





