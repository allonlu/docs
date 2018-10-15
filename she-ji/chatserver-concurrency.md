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
    - `ChatContent`  - 包含原先sessionChat表中的chatContent字段内容, 去除json格式中的tag, 时间等保留有效的聊天内容
    - `CustomFeilds` - Chat中的所有CustomFields使用换行符连接以后放到该字段中

  - t_LiveChat_OfflineMessageFullText
    - `SiteId`
    - `OfflineMessageId`
    - `Title`
    - `Content`
 