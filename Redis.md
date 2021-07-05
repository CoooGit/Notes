# Redis笔记

## Redis基本概念

Redis即Remote Dictionary Service，远程字典服务，是一种广泛使用的开源中间件。单节点的Redis可以理解为一个基于内存的、可持久性的键值对存储数据库。

## Redis支持的数据结构

Redis支持的数据结构共有5种：字符串string、列表list、字典hash、集合set、有序集合zset。

- string

  Redis中的字符串是动态字符串，类似Java中的ArrayList，采用预分配冗余空间的方式减少内存的频繁分配；字符串最大长度为512MB。

  Redis字符串最常见的用途就是缓存用户信息，将用户信息结构体使用JSON序列化为字符串，然后将字符串放入Redis来缓存。

- list

  Redis中的列表相当于Java中的LinkedList，即实现方式是链表而不是数组，其插入和删除操作复杂度为O(1)而查找复杂度相对较高，为O(n)。列表中的每个元素都使用双向指针指向前后节点。

  Redis中的列表常用作异步队列使用，将需要延后处理的任务结构体序列化为字符串，放入Redis列表，再用一个线程从列表中轮询处理。

- hash

  Redis中的字典相当于Java中的HashMap，是一个无序字典，实现结构上与Java的HashMap也是基本类似的，采用数组+链表的二维结构，但Redis的字典进行rehash的过程与HashMap不同，HashMap会一次性完成rehash操作，而Redis采用渐进式rehash，同时保留新旧两个hash结构，在后续的定时任务和hash操作中，逐渐将旧hash的内容转移到新hash中。

  hash同样可以作为缓存使用，与string不同，hash可以对其维护的各个字段单独获取。而使用string实现缓存时需要一次读取全部内容，造成网络流量的浪费。

- set

  Redis中的集合类似Java中的HashSet，其内部的键值对是无序的、唯一的，可以看作一个特殊的字典，其所有的value都是NULL。

  set结构的使用多是利用其去重的特性，如存储某活动的中奖用户id，保证同一用户不会中奖2次。

- zset

  zset是Redis自定义的数据类型，类似Java中的SortedSet和HashMap的结合体。