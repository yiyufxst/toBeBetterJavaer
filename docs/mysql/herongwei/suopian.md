### 54、说一下 MySQL 的锁

- MySQL 在 server 层 和 存储引擎层 都运用了大量的锁
- MySQL server 层需要讲两种锁，第一种是MDL(metadata lock) 元数据锁，第二种则 Table Lock 表锁。
- MDL 又名元数据锁，那么什么是元数据呢，任何描述数据库的内容就是元数据，比如我们的表结构、库结构等都是元数据。那为什么需要 MDL 呢？
- 主要解决两个问题：事务隔离问题；数据复制问题
- InnoDB 有五种表级锁：IS（意向读锁）；IX（意向写锁）；S（读）；X（写）；AUTO-INC
- 在对表进行select/insert/delete/update语句时候不会加表级锁
- IS和IX的作用是为了判断表中是否有已经被加锁的记录
- 自增主键的保障就是有 AUTO-INC 锁，是语句级别的：为表的某个列添加 AUTO_INCREMENT 属性，之后在插⼊记录时，可以不指定该列的值，系统会⾃动为它赋上单调递增的值。
- InnoDB 4 种行级锁
- RecordLock：记录锁
- GapLock：间隙锁解决幻读；前一次查询不存在的东西在下一次查询出现了，其实就是事务A中的两次查询之间事务B执行插入操作被事务A感知了
- Next-KeyLock：锁住某条记录又想阻止其它事务在改记录前面的间隙插入新纪录
- InsertIntentionLock：插入意向锁;如果插入到同一行间隙中的多个事务未插入到间隙内的同一位置则无须等待
- 行锁和表锁的抉择
  - 全表扫描用行级锁

### 55、什么是幻读？

值在同一个事务中，存在前后两次查询同一个范围的数据，第二次看到了第一次没有查询到的数据。

幻读出现的场景：

- 事务的隔离级别是可重复读，且是当前读。
- 幻读指新插入的行。

幻读带来的问题：

- 对行锁语义的破坏
- 破坏了数据一致性

解决：

- 加间隙锁，锁住行与行之间的间隙，阻塞新插入的操作。
- 带来的问题：降低并发度，可能导致死锁。