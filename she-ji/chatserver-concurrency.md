# 移除锁中的数据库操作
  - 


# 全文索引改进

全文索引手动更新导致Chat表被堵住, 从而导致整个服务器的吞吐量下降

| Task | Granted Resource | Waiting Resource | Time | Des |
| :- | :- | :- | :- | :- |
| Search History/ Report | session_chat_table_schema - I | - | 30s or more | 查询历史/报表速度会比较慢, 往往会执行较长的时间 |
| Fulltext index Population | - | session_chat_table_schema - X | waiting | 手动填充会对表上架构排他锁 |
| Request Chat | page_visit_lock - Write | session_chat_table_scehma - I | waiting | Session Chat表的架构共享锁无法申请到, 因为排他锁优先处理 |
| Other Visitor/Agent Actions | - | page_visit_lock - Read | waiting | 有很多线程在这边等待分配锁资源, 当工作线程耗尽以后, 所有请求会进入队列 |

从上述列表来看, 可以通过修改全文索引的方案来打破上述资源争用情况, 从而提高服务器吞吐量

- 将LiveChat中做全文索引的字段放到另外的表中, 以下表都为500个站点一个表, 如果一个站点一个表, 全文索引的表太多的话在上线时会出现内存被填充完全消耗掉, Sql Server无法使用的问题

  - t_LiveChat_ChatFullText
    - `SiteId`
    - `ChatId`
    - `Time`
    - `ChatContent`  - 包含原先sessionChat表中的chatContent字段内容, 去除json格式中的tag, 时间等保留有效的聊天内容
    - `CustomFeilds` - Chat中的所有CustomFields使用换行符连接以后放到该字段中
    

  - t_LiveChat_OfflineMessageFullText
    - `SiteId`
    - `OfflineMessageId`
    - `Title`
    - `Content`

- 全文索引表的信息维护, 使用Service Broker做异步的解耦, 聊天保存以后生成一个message, 另一个模块再去定时操作维护全文索引的相关数据, 发布时候的数据库升级也可以采用这种方案
- 查询中使用新的全文索引
- 删除SessionChat表和SessionOfflineMessage表中的全文索引


## Sql Server full-text index

  - 文档看一下fulltext index的改进方案

  - 全文索引, 手动填充 Sql Server 无法使用的问题
    - 创建表的时候没有设置 no population 会触发完全填充

## Reids
- Redis 数据持久化是否是事务性的
  - 
- Redis MQ


## Chat Server 解耦

使用SQL Server中的方案, 无Server的设计

1. 数据存储模块独立, 进程外

2. 消息队列解耦

3. 有数据库运行

4. 升级方案, 各种情况的用户升级体验, 升级体验的差别, 编码/发布的区别 ******
  - 无数据库运行, 用户的升级体验，我们的开发编码
  - 用maxon升级, 用户的升级体验，我们的开发编码

## Maximum On 所有功能都可以用

MaximumOn的功能做一下
	i. 功能完备
	ii. salesforce, SSO


## KB灾备
- Cloudflare
  Loadbalance, 主从关系
- 数据同步
  - Sql Server 同步
  - 程序同步

## 
6. 站点合并方案
## full-text
### 新建脚本
1. Change_Tracking Off, NO Population 不会填充
2. 

1. 自动, 一个站点一个库, x
2. 手动
3. off, 增量
4. 自动, 一个站点一个库, x
5. 


change tracking 的原理
手动/增量
2016 手动更新是否会锁表
