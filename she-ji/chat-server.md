

# chat server 解耦

## 现有状况

目前ChatServer作为LiveChat的核心模块，为访客端和客服端提供服务，处理聊天的业务，机器人服务对接，集成其他产品等多项业务。在这样的状况下，问题也比较明显，Chatsever作为各种业务耦合的一个单一应用，目前产品功能版本如果涉及到一部分局部功能的变动，也需要进行Chatserver的修改和测试，这里会有多个风险:
1.  项目周期长；
2.	回归测试不完备；
3.	Chatserver涉及的逻辑多，开发人员难以维护；
4.	Chatserver本身的负载高，

## 架构目标

1.	让Chatserver成为一个无状态(状态转移到缓存服务器中供各个程序共享的应用，支持负载均衡)
2.	将Chatserver中的同步IO操作采用异步操作

### 总体思路

1.	使用缓存服务器, 将原来ChatServer内存中的数据放到缓存服务器中, 使Chat Server应用可以支持负载均衡, 可以快速重启
2.	使用消息队列解耦Chat Server中的一部分异步操作
3.	将Chat Server模块根据业务进行拆分,   将访客/聊天等核心模块与其他非核心模块分离, 保证核心模块可以在没有数据库的状态下运行, 使得在数据库升级过程中可以使用

### 拓扑结构

![topology](topology.png)

1. 在MaxOn功能基础上, 部署分为主服务器, 以及备用服务器, 异地跨机房部署
2. 主服务器上的各个主机在一个局域网内
  - 多套Web Server做Load-balance, 对外使用不同的IP, 可以在Load-balance控制器中控制流量到哪个平台
  - Redis Server在本地Master-slave, 
  - 本地有一个MQ Server可以做消息队列管理
  - 本地有一个数据库服务存储数据
3. 副服务器上部署一套程序
  - 部署一套Web应用
  - Redis服务从主服务器的Redis - Salve同步数据
  - DB Server仅存储应用所需的配置数据
  - 本地的MQ Server作为副服务器的消息队列管理
4. 远程有一个MQ Service, 可以在本地消息服务失效时使用(MaxOn 主副使用同一个远程的MQ Server)


## ChatSrver架构 

  ![chatserver](chatserver-arch.png)

### 模块说明

#### Chat Server 
Chat Server应用程序分5个模块: API, Process, Caching, EventCenter, Asynchronous Messages
1. API
  - 提供Visitor, Agent, Chat  的接口
2. Process
  - 处理Visitor, Agent, Chat的请求
3. EventCenter
  - 注册/分发ChatServer中的事件
4. DataAccess
  - 数据访问层, 在其之下有可能是访问数据库/内存/StateService/MQ

#### Adapater Service

支撑Chat Server功能的其他一些服务，主要是访问第三方服务, 处理返回
1. Salesforce Service
  - salesforce相关功能的一些服务, 由客户端调用
2. Bot Chat Adapter
  - bot相关的功能, 监听ChatServer中的一些事件, 响应事件以后调用bot api, 返回结果插入到聊天消息中

#### MQ Proxy

消息队列的代理服务, 可以将消息插入到本地的消息队列服务器中, 在本地消息队列不可用时插入到远程消息队列服务器中

1. Message compensation
  - 消息的补偿服务, 主要是拉取远程的消息队列服务器，插入到本地的消息队列中
  - 使用唯一的MessageId 防止消息重复插入

2. Subscription Service
  - 消费消息的服务, 做一些异步操作， 主要为持久化, 第三方集成等

#### 缓存服务

这是以Redis 为代表的进程外状态服务器, 在系统中为可插拔状态, 即Caching模块可以不使用进程外的状态服务器而使用进程内的内存来管理状态

## Load-balance
1. Chat Server应用本身没有状态, 可以部署多套程序指向统一的缓存服务作为进程外的状态服务
2. 在Load-balance控制器中可以直接做active-active的配置
3. 单个Chat Server 应用程序作为Load-balance下面的一个节点可以关机, 使流量导向其他现在可用的节点
4. 多个Chat Server应用访问同一个缓存服务器, 可以使用乐观锁保证事务性

## MaxOn 与 缓存服务
1. MaxOn 副服务器中对应的缓存服务从主服务器的缓存服务中同步
2. 副服务器中的缓存服务开启持久化功能, 使得重启以后数据能够恢复
3. 在副服务器不是active的时候, Chat Server中的时钟停掉, 避免产生重复的消息而产生重复的邮件/Salesforce Case等.
4. 在副服务器切换为active的时候, 停止缓存服务从主服务器的缓存中同步, 避免老数据同步过来
5. 服务器切换过程中, 客户端都需要跟Server进行一次数据同步, 以保证客户端与服务器的状态一致
6. 在One MaxOn的Server上, 副服务器需要对应多个缓存服务

## 应用发布升级

1. 直接发布程序, 应用重启, IIS会处理请求不会因为重启而失败
  - 旧的请求会在旧的程序中处理, 待旧的请求处理完以后, 进程会关掉
  - 新的请求会在新程序启动以后再处理

2. Load-balance切换
  - 将要升级的Server在Load-balance控制器中将流量移掉
  - 升级这个Server, 待测试通过以后, 将流量导入到升级以后的Server中
  - 待确认升级的Server没有问题以后, 再通过Load-balance将另外的Server再一个一个升级

3. MaximumOn切换
  - 采用maximumOn切换到副服务器发布
  - 主服务器升级以后, 将服务器切换回主服务器

### Redis数据变更影响

修改Redis数据对象，对象采用hash类型， 新程序对对象做如下变更
  1.	增加对象 – 可以同时兼容新老程序，只需要修改用到这个字段的模块
  2.	对象增加字段 – 可以同时兼容新老程序， 只需要修改用到这个字段的模块
    - 新程序模块读取老程序模块保存的对象，在反序列化时为新字段添加默认值
    - 老程序模块读取新程序模块保存的对象，反序列化的时候会忽略新添加的字段
    - 老程序模块修改新程序模块保存的对象，只会修改自己定义的那些字段，不会修改添加的字段
    - 新程序模块修改老程序模块保存的对象，完整保存新的对象
  3.	修改基本数据类型的字段, 因为实际存储都是以string来存储, 因为在类型转化没有问题的前提下可以完全兼容新老版本，需要修改用到这个字段的模块
  4.	修改对象类型的字段(增加属性)，因为针对这种类型是通过json存储, 应此需要将使用到这个对象的所有模块同步修改
  - 新程序访问老程序的数据，在反序列化时添加默认值
  5.	删除对象的字段，因为删除上一个版本在使用的字段可能导致程序回滚失败, 因此只能删除已经确定不用的，在这种情况下也可以同时支持新老程序

