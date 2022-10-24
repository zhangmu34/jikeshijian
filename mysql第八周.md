### **题目 01- 完成 ReadView 案例，解释为什么 RR 和 RC 隔离级别下看到查询结果不一致**

1. **案例 01- 读已提交 RC 隔离级别下的可见性分析**

**目标**

证明RC 和 RR 隔离级别的差异

**操作步骤**

| 时间 | 事务1  RC  | 事务2  RC    | 事务3   RC               |
| ---- | ---------- | ------------ | ------------------------ |
| T1   | 开启事务   | 开启事务     | 开启事务                 |
| T2   | 更新为关羽 |              |                          |
| T3   | 更新为张飞 |              |                          |
| T4   |            | 更新为赵云   |                          |
| T5   |            | 更新为诸葛亮 |                          |
| T6   |            |              | SELECT01，id=1，name为？ |
| T7   | 提交事务01 |              |                          |
| T8   |            |              | SELECT02，id=1，name为？ |
| T9   |            | 提交事务02   |                          |
| T10  |            |              | SELECT03，id=1，name为？ |



**实践过程**

1.

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-22_17-59-25.png)

2.

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-22_18-08-46.png)

3.

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-22_18-10-25.png)

4.

按照顺序执行到T6，查询信息为**刘备**

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-22_18-50-35.png)

5.

T8时间段，事务1已提交，查询信息为**张飞**

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_00-02-40.png)

6.

T10时间段，事务2已提交， 查询信息为**诸葛亮**

![Snipaste_2022-10-22_17-59-25](https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_00-04-39.png)

**结论**

RC可以查看到每次事务提交后生成的新的，未提交的无法看到

2. **案例 02- 可重复读 RR 隔离级别下的可见性分析**

**目标**

证明RC 和 RR 隔离级别的差异

**操作步骤**

| 时间 | 事务1  RC  | 事务2  RC    | 事务3   RC               |
| ---- | ---------- | ------------ | ------------------------ |
| T1   | 开启事务   | 开启事务     | 开启事务                 |
| T2   | 更新为关羽 |              |                          |
| T3   | 更新为张飞 |              |                          |
| T4   |            | 更新为赵云   |                          |
| T5   |            | 更新为诸葛亮 |                          |
| T6   |            |              | SELECT01，id=1，name为？ |
| T7   | 提交事务01 |              |                          |
| T8   |            |              | SELECT02，id=1，name为？ |
| T9   |            | 提交事务02   |                          |
| T10  |            |              | SELECT03，id=1，name为？ |

实践过程

1.

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_21-36-13.png

2.

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_21-37-45.png

3.

T6时间 ，查询为**刘备**

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_21-38-43.png

4.

T8时间 ，查询为**刘备**

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_21-40-29.png

5.

T10时间 ，查询为**刘备**

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_21-41-33.png

**结论**

无论事务1和事务2是否提交，都不影响事务3查询结果

**结论分析**

RC隔离级别的事务在每次查询开始时都会生成一个独立的ReadView。

RR 隔离级别的事务，只会在第一次执行查询语句时生成一个 ReadView ，之后的查询就不会重复生成了。

RC、RR这两个隔离级别的一个很大不同就是生成 ReadView 的时机不同。

### 题目 02- 什么是索引？

索引是mysql高效获取数据的数据结构，就像书的目录一样，加快数据库的查询速度。

**优点是什么？**

可以提高数据检索的效率，降低数据库的IO成本，类似于书的目录。
通过索引列对数据进行排序，降低数据排序的成本，降低了CPU的消耗。

**缺点是什么？**

索引会占据磁盘空间
索引虽然会提高查询效率，但是会降低更新表的效率。

**索引分类有哪些？特点是什么？**

单列索引：索引中只有一个列。

组合索引：使用2个以上的字段创建的索引。

**索引创建的原则是什么？**

频繁出现在where 条件，order排序，group by的字段
Select 频繁查询的列
多表join关联查询on两边的字段

**有哪些使用索引的注意事项？**

表记录很少不需要创建索引
单表索引数量不要太多，单列变组合
频繁更新的资源不建议索引
区分度不高、无序的字段不建议索引
很长的字段不建议使用索引，最好使用自增长型字段作为索引
尽量创建组合索引，而不是单列索引。
创建组合索引原则：组合索引把频繁使用的列、区分度高的列放在前面

**如何知道 SQL 是否用到了索引？**

sql语句前加explain，type里的结果值就是是否使用索引

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-24_18-26-08.png

**请你解释一下索引的原理是什么？【重点】**

索引是一个B+Tree树

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-24_18-37-36.png

只有叶子节点才会存储数据，非叶子节点只存储键值。叶子节点之间使用双向指针连接，最
底层的叶子节点形成了一个双向有序链表。

**说清楚为什么要用 B+Tree**

1.Hash表

不支持范围快速查找，范围查找时还是只能通过扫描全表方式。
数据结构比较稀疏，不适合做聚合，不适合做范围等查找。

2.二叉查找树

极端情况下，二叉查找树会构建成为单向链表 = 查找全表扫描。

3.红黑树

时间复杂度和树高相关：树有多高就需要检索多少次，每个节点的读取，都对应一次磁盘 IO 操作

平衡二叉树不支持范围查询快速查找，范围查询时需要从根节点多次遍历，查询效率极差。

数据量大的情况下，索引存储空间占用巨大

4.B树，改进二叉树，为多叉树

优点：
磁盘IO次数会大大减少。
比较是在内存中进行的，比较的耗时可以忽略不计。
B树的高度一般2至3层就能满足大部分的应用场景，所以使用B树构建索引可以很好的提升查询的
效率。
缺点：
B树不支持范围查询的快速查找：如果我们想要查找15和26之间的数据，查找到15之后，需要回到
根节点重新遍历查找，需要从根节点进行多次遍历，查询效率有待提高。
空间占用较大：如果data存储的是行记录，行的大小随着列数的增多，所占空间会变大。一个页中
可存储的数据量就会变少，树相应就会变高，磁盘IO次数就会变大。

5.B+树：改进B树，非叶子节点不存储数据

继承了B树的优点【多叉树的优点】
保证等值和范围查询的快速查找

所以选择B+树



### 题目 03- 什么是 MVCC？

MVCC全称叫多版本并发控制，核心是读不加锁，读写不冲突，大大提高了并发性能。

MVCC 的实现依赖是Undo日志 与 Read View

**Undo 日志**

回滚日志，Undo日志保存了记录修改前的快照，在操作失败需要回滚的时候，用undo信息回滚到修改之前的样子，有了Undo日志可以做到读不加锁，读写不冲突。

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_22-21-58.png

根据行为的不同Undo日志分为两种： Insert Undo Log 和 Update Undo Log

Insert Undo日志：是在Insert操作中产生的Undo日志

Insert 操作的记录只对事务本身可见，对于其它事务此记录是不可见的，所以 Insert Undo Log 可以在
事务提交后直接删除而不需要进行回收操作。

Update Undo日志 ：是Update或Delete 操作中产生的Undo日志

Update操作会对已经存在的行记录产生影响，为了实现MVCC多版本并发控制机制，因此Update Undo
日志不能在事务提交时就删除，而是在事务提交时将日志放入指定区域，等待 Purge 线程进行最后的删
除操作。



对于使用 RU 隔离级别的事务来说，直接读取记录的最新版本就好了，不需要Undo log。
对于使用 串行化 隔离级别的事务来说，使用加锁的方式来访问记录，不需要Undo log。
对于使用 RC 和 RR 隔离级别的事务来说，需要用到undo log的版本链。

**ReadView**

ReadView是张存储事务id的表，主要包含当前系统中有哪些活跃的读写事务，把它们的事务id放到一个
列表中。结合Undo日志的默认字段【事务trx_id】来控制那个版本的Undo日志可被其他事务看见。

https://github.com/zhangmu34/jikeshijian/blob/main/8/Snipaste_2022-10-23_22-22-26.png

m_ids：表示在生成ReadView时，当前系统中活跃的读写事务id列表

m_low_limit_id：事务id下限，表示当前系统中活跃的读写事务中最小的事务id，m_ids事务列表
中的最小事务id

m_up_limit_id：事务id上限，表示生成ReadView时，系统中应该分配给下一个事务的id值

m_creator_trx_id：表示生成该ReadView的事务的事务id



ReadView怎么产生，什么时候生成？
开启事务之后，在第一次查询(select)时，生成ReadView
RC 和 RR 隔离级别的差异本质是因为MVCC中ReadView的生成时机不同。

**如何判断可见性**

开启事务执行第一次查询时，首先生成ReadView，然后依据Undo日志和ReadView按照判断可见性，

按照下边步骤判断记录的版本链的某个版本是否可见。

循环判断规则如下：

- 如果被访问版本的 trx_id 属性值，小于ReadView中的事务下限id，表明生成该版本的事务在生成 ReadView 前已经提交，所以该版本可以被当前事务访问。
- 如果被访问版本的 trx_id 属性值，等于ReadView中的 m_creator_trx_id ，可以被访问。
- 如果被访问版本的 trx_id 属性值，大于等于ReadView中的事务上限id，在生成 ReadView 后才产生的数据，所以该版本不可以被当前事务访问。
- 如果被访问版本的 trx_id 属性值，在事务下限id和事务上限id之间，那就需要判断是不是在m_ids 列表中。
  - 如果在，说明创建 ReadView 时生成该版本的事务还是活跃的，该版本不可以被访问；
  - 如果不在，说明创建 ReadView 时生成该版本的事务已经被提交，该版本可以被访问。

循环判断Undo log中的版本链某一的版本是否对当前事务可见，如果循环到最后一个版本也不可见的

话，那么就意味着该条记录对该事务不可见，查询结果就不包含该记录。
