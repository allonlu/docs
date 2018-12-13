
# webhook event

webhook event统一采用 noun.verb 的形式来定义

1. chat.start
2. chat.end
3. chat.messageReceive
4. chat.fileUpload

# webhook type

- async
  - 该类型的webhook异步执行, 可以放到消息队列中
  - 调用方不需要处理用户的response, 只需要保证成功(status: 2xx)即可
  - 如果调用不成功需要重试
- await (unique)
  - 未防止第三方调用, 该类型webhook超时时间短, 失败不会重试
  - 同一个event只允许配置一种await类型的webhook
  - 调用方可以异步执行, 但不能放到消息队列中, 需要保证实时性
  - 调用方必须处理response, response的内容根据Comm100定义的规则(actions)可以处理一些事情

# webhook response

1. webhook response 可以根据以下规则返回, Comm100会解析返回的内容, 执行特定操作

```json
{
  "error": "",
  "preventDefault": false,
  "actions": [
    {
      "type": "chat.response",
      "payload": {
        "sender": "bot",
        "messageId": "17",
        "content": "The weather is good today?",
      }
    },
    // more actions
  ]
}
```

- `error`, 可以返回错误, server可以记录这些错误, 在某些特定的event可能直接返回到客户端, 如(chat.fileUpload)
- `preventDefault`, 是否阻止默认行为
- `actions`, 调用放会执行的操作, 可以为多个
  - `type`, action 的名字
  - `payload`, 载荷, 可以放一些执行这个action的一些数据

## chat actions

 chat下面的action只允许在chat的event下面才可以响应, 响应的范围也是在该chat下面

  1. chat.addMessage, 可以在聊天中插入一条消息
    - `sender`
    - `content`
  2. chat.response, 可以对指定的一条消息进行回复, 这条消息会插到指定的消息下面
    - `sender`
    - `messageId`
    - `content`
  3. chat.end, 可以结束当前聊天


