# 基于Token的无状态验证

1. 可跨域访问


SignalR: (100万)
10万个连接会怎么样?
连接断开以后远程推会怎么样?
Web Server是异步模式还是同步模式
    - 异步编程 - 
    - 
长连接跟短连接的编程差异是不是很大
    - 如果要转成长连接，Server端的API不开放
    - 

压力测试 - 很多的连接

# API

1. Chat Server API
2. 进程外的状态API设计
3. 

# full-text index
1. 单表在不同数据量的情况下的增量填充效率
    - 1000000 条数据， 插入10000条数据
    - 10000000 条数据，插入10000条数据
2. 500个站点一张表的情况下, 数据更新完成, 自动填充模式
    - 重启数据库
    - 10000000 条数据下, 插入一条数据的填充效率
3. 400个库, 有无全文索引的启动效率
4. 小字段的全文索引 (name/email)
5. Keyword查询效率


Step1: 数据库操作从锁中移除
Step2: 数据库操作异步