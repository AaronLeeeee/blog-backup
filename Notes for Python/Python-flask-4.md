---
title: 关系型数据库在 Flask 中的应用
date: 2015-12-18 14:25:12
tags: python 
categories: About Python
---

* 了解一些关系型数据特点
* 了解关系型数据库在 Python 平台下的应用
* 设计表之间的关联关系，实现复杂的查询操作等

<!-- more -->

### 一对多关系

如下图所示，roles 表存储所有可用的用户角色，每个角色都有**唯一**一个 id 值即主键进行标示，users 表包含用户表，每个用户也有唯一的 id 值，在 users 表中还有其他的字段。users 表中的 user_id 列是外键，**引用**角色的 id，通过这种方式为每个用户指定角色。

![](http://7xrl8j.com1.z0.glb.clouddn.com/%E4%B8%80%E5%AF%B9%E5%A4%9A%E5%85%B3%E7%B3%BB.jpg)

### 多对多关系

多对一以及一对一都可以从一对多关系中引出来，但是多对多就不能简单的这样处理，这种关系两侧都有多个记录。一个场景就是**学生和所选课程**之间的数据关系表示，明显学生有多个，课程也有多门。显然不能在学生表中加入一个指向课程的外键，因为一个学生可以选择多门课程，一个外键不够用，与上述一对多的关系不同，一个用户只能是一种角色，而一个学生可以选择多门课程。当然也不能在课程中添加外键，一门课程有多个学生选择。两侧**都需要一组外键**，解决的办法就是添加第三章表，称为**关联表**，如下图所示：

![](http://7xrl8j.com1.z0.glb.clouddn.com/%E5%AD%A6%E7%94%9F%E4%B8%8E%E8%AF%BE%E7%A8%8B.jpg)

其中的关联表为 registerations 表中每一行数据都是一个学生注册的一个课程。查询多对多的关系过程是：先根据学生和注册之间的一对多关系开始，获取**这位**学生在 registerations 表中的所有记录，然后再按照多对一的方向遍历课程和注册之间的一对多关系，找到这位学生在 registerations 表中 各记录所对应的课程名字。

### 自引用关系

**多对多之间的关系**可以实现用户之间的关注，但是上面的多对多的例子，是基于学生和课程两个实体，但是表示用户之间的关注时，只有一个实体，即关系的两侧都在同一个表中，这种关系称我自引用关系，在关系的左侧是用户实体，关系的右侧也是用户实体。有点难易理解，但是实际山没什么难度。



如下图：

![](http://7xrl8j.com1.z0.glb.clouddn.com/%E8%87%AA%E5%BC%95%E7%94%A8%E5%85%B3%E7%B3%BB.jpg)

### 高级多对多关系

自引用关系可以实现用户之间的关注，但是由于基于一个表，我们不能在添加额外的信息，比如在关注的情况下，要记录关注的时间，然后根据关注的时间列表列出所有的关注者，这种信息只能存储在**关联表**中,之间学生和课程之间的关系中，关联表完全由 SQLAlchemy 掌握的，是其内部表，为了能**自定义关联表的数据**，必须提升表的地位，即建立一个**实体模型**出来

### 使用数据库联合查询所关注用户的文章

目的很简单，就是能够在用户的主页显示，自己关注的用户的文章。最开始的想法肯定是获取关注的用户列表，然后获取用户的文章，写入单独列表，明显随着数据量的不断增加，列表不断加长，效率可想而知。

完成这个操作的最好的办法就是**联结**，需要两个或者更多的数据表，在其中查找满足指定条件的记录组合。并插入一个临时表中，其即为 联结查询的结果。

### 评论在数据库中的表示

![](http://7xrl8j.com1.z0.glb.clouddn.com/%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0%E8%AF%84%E8%AE%BA.jpg)