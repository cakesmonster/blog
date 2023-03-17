
# MySQL面试题

mysql事务，acid，实现原理，脏读，脏写，隔离级别，实现原理，mvcc，幻读，间隙锁原理，什么情况下会使用间隙锁，锁失效怎么办，其他锁了解么，行锁，表锁


MySQL的锁 表锁 行锁 间隙锁 next-key锁


mysql索引左前缀原理，怎么优化，哪些字段适合建索引，索引有什么优缺点。mysql中有一个索引(a,b,c)，有一条sql，where a = 1 and b > 1 and c =1;可以用到索引么，为什么没用到，B+树的结构，为什么不用红黑树，B树，一千万的数据大概多少次io



mysql主键索引，没有主键怎么办，会自己生成主键为什么还要自定义主键，自动生成的主键有什么问题？主键可以为空吗？可以允许几个？主键跟非主键的区别？索引存储形式？


数据库创建索引的指令


Innodb的结构，磁盘页和缓存区是怎么配合，以及查找的，缓冲区和磁盘数据不一致怎么办，mysql突然宕机了会出现数据丢失么


oracle跟mysql的区别，隔离级别

mysql索引，覆盖索引？回表？B+树叶子节点存储什么？为什么不用AVL树？


如何避免回表？使用覆盖索引


索引下推


查询指令遇到瓶颈后如何优化


B+树为什么数据项不放到非叶子节点呢，B+树查找效率高体现在哪里

唯一索引和普通索引的区别

