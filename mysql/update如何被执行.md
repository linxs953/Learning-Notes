## update语句如何被执行



- redo log（存储层的重做日志，记录具体的物理变更）
  - wal，write ahead logging，写磁盘前写记录
    - 当有一条记录需要更新当时候，先写到redo log，并更新内存，然后存储引擎在系统空闲当时候sync to disk
  - redo log有固定大小，写满会覆盖，that is to say，如果redo log的大小为4G，那么它最多只能保存最新的大小为4G的内容
  - 有了redo log，mysql就可以在发生异常重启的时候，保证之前提交的记录不丢失，也被成为crash-safe


- binlog(server层的归档日志，只记录逻辑变更)
  - 为什么需要binlog和redolog两份日志
    - 早先mysql只有MyISAM引擎，只有binlog，没有crash-safe能力
    - innodb是以插件形式进入mysql的，需要一套日志系统来保证mysql的crash-safe

- redolog和binlog区别
  - redolog是innodb独有的，binlog是server层实现的，所有引擎都可使用
  - redolog是物理日志，记录“在哪个数据页进行了什么修改”，binlog是逻辑日志，记录这条语句的原始逻辑，“给id=2的记录的c字段+1”
  - redolog会循环写，binlog是追加的，不会覆盖原有内容

- 两阶段提交
  - 记录redolog和binlog分为两次提交
    - 原因：为了让两个log之间的逻辑保持一直
    - 不用两阶段问题
      - 先写redolog再写binlog
        - 写完redolog之后mysql异常重启，binlog还未写进去
        - 重启后的mysql可以通过redolog恢复这一次更新的数据
        - 如果是后续想要用binlog备份临时库的话，就会少了这一次更新，因为当前的redolog和binlog逻辑不一致
      - 先写binlog再写redolog
        - 写完binlog后mysql退出，redolog未写进去
        - 重启后的mysql无法恢复之前的事务提交的数据
        - 如果之后使用binlog来恢复的话就多了一个事务出来，与原库的值不同
