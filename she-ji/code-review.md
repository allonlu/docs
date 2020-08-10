# Code Review
1. chat server的地址为什么要把https替换为http， 应该是要保持
2. reader使用using会更好
3. 在catch中将exception抛出的时候需要直接使用throw， 而不要throw exp；
4. timer可以使用framework中的公共方法创建
5. Change Log如果删除的数据比较多可以考虑记录reload的action
6. 访客端那边的enum不需要由客户端转换，而是使用server端的类型直接反序列化
7. EnumChatAction定义了两个，建议去掉一个使各个地方统一
8. 新的访客端兼容老的服务器，在收到1004报错的时候，在老的服务器时不要做恢复动作
    - 访客从visitorId -> visitorGuid做migration的部分逻辑要重点review一下
9. eventname定义枚举，存库是还是字符串
10. MQ的Master和Slave的逻辑需要改动一下，如果主的写不进去就写副的，不需要维护现在的主副状态
11. MQ的重试机制应该是放到一个队列中
12. 恢复， 访客的状态不需要再维护一个状态， 而是原来的状态就可以转换成当前的状态。
13. 回迁程序需要回迁文件 - 考虑可以使用mq写回来


# Test Review
1. Agent删除/Inactive, Site Closed等相关的Agent需要登出
2. Token那部分的测试用例review一下，要可执行的用例
3. MQ消费不成功的重试机制需要重新设计, 副服务器的MQ方案需要重新设计
4. VUE响应BBS Code时使用siteId每次都访问moderator, 可以考虑Server端做一个5s的缓存



# Channel Review
1. App 下面的id去掉, 可以直接使用App Id作为主键
2. Secret Key应该为Oauth client的client_secret，与Oauth Client下面的id是在同一个表中
3. Name 需要跟产品确定长度
4. Contact Identity Type的id不需要?
5. Message display Type 下面的IM应该是不合适的，需要跟产品确认一下
6. Message Max length应该是只控制发消息的长度，名字改为Outgoing message max length
7. Message Scope的名字修改为 Outgoing message capability， 下面的plainText修改为text
8. app的升级体验，public状态等
9. Access Token和Refresh Token过期以后需要重新授权


# Code Review
1. needRecover的报错不要记录日志
2. VisitorSetEmail的chatGuid需要从visitor 传过来
    - 恢复的时候邮件是不是会发？
3. Statistic不用上锁，直接Count一下就可以了
4. 工作线程数的代码可以去掉了，现在线上实际上已经不使用了
5. IfSearchingKB的set设置成private
6. SiteObject - OneMaxOnSite的对象用了cache的过期机制 - 应该采用ChangeLog来做，副服务器同步数据的时候需要写这个change log
7. MQ 的Performance Counter在WriteMessage里写就可以了
8. Visit Times应该是访客端加好以后再传过来，newVisitor和RecoverVisitor那边不需要再维护


# 发布流程Review
提前准备
- 生成维护的库
- General 库做全库备份

1. 提前部署好免费平台，部署在hosted那台机器上:hosted5.comm100.com, freechatserver.comm100.com(程序转成应用程序)
2. chatserver.comm100.com 那台服务器上发布一个vue.comm100.com, 绑定vue.comm100.com, chatserver5.comm100.com的域名
3. 部署vue1.comm100.com - 在哪台机器上可以跟IT商量？？
4. 做好hosted5.comm100.com, freechatserver.comm100.com, vue.comm100.com, vue1.comm100.com的域名解析， 跟IT确定一下是否配置在cloudflare 下面，提前做好
5. 将hosted5.comm100.com全部跳转到维护页面
6. 手机app release


开始发布：
1. 手动切换收费chatserver到副服务器
2. 发布routeserver的bin
3. 发布chatserver.comm100.com的web.config
    - web.config配置了urlrewrite，将访客端的请求rewrite到vue.comm100.com
3. 发布hosted.comm100.com
    - 配置url rewrite将免费站点rewrite到hosted5.comm100.com

4. 对General库做差量备份
5. 收费chatserver启动完成以后将收费chatserver切回到主服务器


5. 启动免费的维护程序
6. 删除原freegeneral库 - 这个库里有什么内容吗？是否需要将数据迁到新的General库
7. 还原收费库的general库到freegeneral库
8. 数据库升级
    - 免费数据库升级，（包括freeGeneral库的配置修改）
    - route库配置修改
    - freeGeneral删除收费数据
9. 发布agent console桌面版
10. 发布hosted5.comm100download.com
11. 发布pciform.comm100.com
9. hosted5.comm100.com去掉跳转维护页面
10. 停维护程序,回迁留言数据


# Guid的改动
1. API
  - chat
    - 可以同时接受id和guid
  - message
  - visitor
2. webhook
  - 将id字段的类型改为string， 赋上guid的值
  -

# WAF 对消息进行base64编码
  - 站点增加MessageEncode字段, 

- ChatServer - Yan
- 


# Status Recovery Review

1. ChatEnded状态，访客端和Agent端都需要恢复一下聊天
  - 访客端要保持心跳一段时间, 3m以后停止heartbeat
2. 访客端处于Refused by Agent状态， 访客端需要恢复一下聊天
  - 访客端保持心跳一段时间, 3m以后停止heartbeat
3. Agent 恢复Refused by Agent状态，需要恢复一条refused的消息让访客端可以收到refused状态 - 在两端状态不一致的时候有问题（agent还处于waiting状态）
4. 根据聊天消息恢复访客状态
  - 收到SystemEnded的时候更新为ChatEnded
  - 收到RefuseByAgent的消息时更新状态到RefuseByAgent
5. UnfinishedWrapup Server需要恢复一下

# 手动分站点支持maxOn


## Chat Server
1. ActiveSite中增加一个属性, IfUseStandbyServer


