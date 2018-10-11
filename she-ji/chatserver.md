# Chat Server设计

## 案例

- 访客进站过程

```mermaid
sequenceDiagram
访客->>+访客服务: 下载代码
访客服务-->>-访客: 返回
访客->>+访客服务: 下载campaign
访客服务-->>-访客: 返回campaign
访客->>+访客服务: pageVisitor
访客服务->>+Rule服务: 计算相关rule: segment, dynamicCampaign, route, autoInvitation
Rule服务-->>-访客服务: 返回rule，dynamic campaign结果
访客服务->>+Redis服务: 获取department/agent列表
Redis服务-->>-访客服务: 返回department/agent列表
访客服务->>+Redis服务: 生成访客
Redis服务-->>-访客服务: 返回访客
访客服务-->>-访客: 返回route结果等
访客服务->>消息队列: VisitorCreated
消息队列->>Webhook: VisitorCreated
访客->>+访客服务: 设置custom variables
访客服务->>+Redis服务: 获取访客
Redis服务-->>-访客服务: 返回获取访客
访客服务->>+Redis服务: 获取department/agent列表
Redis服务-->>-访客服务: 返回department/agent列表
访客服务->>+Rule服务: 计算相关rule: segment, dynamicCampaign, route, autoInvitation
Rule服务-->>-访客服务: 返回rule结果
访客服务->>+Redis服务: 更新访客
Redis服务-->>-访客服务: 返回访客
访客服务-->>-访客: 返回route，dynamic campaign结果等
opt 如果campaign有变化
  访客->>+访客服务: 下载campaign
  访客服务-->>-访客: 返回campaign
end
访客-->>访客: 显示聊天按钮
```

- 访客打开聊天窗口并请求聊天

```mermaid
sequenceDiagram
访客->>+访客服务: 点击聊天按钮
访客服务->>+Redis服务: 获取访客
Redis服务-->>-访客服务: 返回获取访客
访客服务->>+Redis服务: 获取department/agent列表
Redis服务-->>-访客服务: 返回department/agent列表
访客服务->>+Rule服务: 计算相关rule: segment, dynamicCampaign, route
Rule服务-->>-访客服务: 返回rule结果
访客服务-->>-访客: 显示聊天窗口
访客-->>访客: kb/prechat

访客->>+访客服务: 提交prechat

访客服务->>+Redis服务: 获取访客
Redis服务-->>-访客服务: 获取访客

opt 如果开启salesforce
  访客服务->>+Salesforce服务: 匹配salesforce数据
  Salesforce服务-->>-访客服务: 返回salesforce数据
end

访客服务->>+Redis服务: 获取department/agent列表
Redis服务-->>-访客服务: 返回department/agent列表

访客服务->>+Rule服务: 计算相关rule: segment, dynamicCampaign, route
Rule服务-->>-访客服务: 返回rule结果

alt 客服在线或者chatbot
  访客服务->>+核心服务: 请求聊天
  核心服务-->核心服务: 分配聊天
  核心服务->>+Redis服务: CreateChat
  Redis服务-->>-核心服务: CreateChat完成
  核心服务-->>-访客服务: 返回
else 客服不在线
  访客服务->>+核心服务: UpdateVisitor
  核心服务->>+Redis服务: UpdateVisitor
  Redis服务-->>-核心服务: UpdateVisitor完成
  核心服务-->>-访客服务: UpdateVisitor完成
end

访客服务-->>-访客: 返回offline, waiting, chatting, chatting with chatbot

核心服务->>消息队列: ChatCreated
消息队列->>Webhook: ChatCreated
```

- 访客聊天过程

```mermaid
sequenceDiagram
loop 每条消息或者心跳
  访客->>+访客服务: 发送消息
  访客服务->>+核心服务: 发送并获取消息
  核心服务-->>-访客服务: 返回消息
  opt 如果和chatbot聊天
  核心服务->>+ChatBot: 通过事件通知
  ChatBot-->-核心服务: 回复
  end
end
```

- 访客结束聊天过程

```mermaid
sequenceDiagram
访客->>+访客服务: 结束聊天消息
访客服务->>+核心服务: 发送结束聊天消息，并获取消息
核心服务->>+Redis服务: UpdateVisitor
Redis服务-->>-核心服务: UpdateVisitor完成
核心服务-->>-访客服务: 返回消息
访客服务-->>-访客: 返回消息
```

- 客服请求语音聊天，访客接受过程

```mermaid
sequenceDiagram
participant Client as Agent/Visitor
participant AS as Agent服务 
participant VS as Visitor服务
participant C as 核心服务
participant Redis as Redis服务
participant AV as 语音视频聊天服务

Client->>+AS: Agent请求语音聊天
AS-xC: 请求语音聊天
deactivate AS
activate C

C-xAV: 转发请求语音聊天请求事件
deactivate C
activate AV
AV->>+Redis: 获取语音聊天状态
Redis-->>-AV: 返回
AV-->>+Redis: 更新语音聊天状态
Redis-->-AV: 更新返回
AV-->C: 返回
deactivate AV
activate C
C-->AS: 返回
deactivate C
activate AS
AS-->Client: 返回
deactivate AS
Client->>+VS: Visitor Heartbeat
VS->>+C: 查新是否有新消息
C->>+Redis: 查询
Redis-->>-C: 返回新的聊天消息
C-->>-VS: 返回新的聊天消息
VS-->-Client: 返回新的聊天消息
```

- conversion

## Redis服务器接口列表

- CreateVisitor
- CreateChat
- CreateAgent
- RemoveVisitor
- RemoveChat
- RemoveAgent
- UpdateVisitor
- UpdateChat
  - SendChatMessage
- UpdateAgent
  - SendAgentMessage
- GetVisitors
  - 可以根据各种条件过滤，guid,site,status等等
- GetAgents
- GetChatMessages
- GetAgentMessages





### feedback
1. Pipeline, 可插拔
2. 事件定义的处理过程 - 
3. 模块发布升级 - loadbalance