
# Webhook

目前只有LiveChat系统中有webhook, 现在把这部分定义提到全局, 可以在系统中定义所有产品的webhook. 针对现在一个webhook只能订阅一个event, 可以考虑将一个webhook订阅多个event.

## Webhook基本属性

  - `events` - `required`, 该webhook订阅的事件, 为一组列表
  - `url` - `required` 该wenhook的远程响应地址
  - `mothed` - 目前均为post请求url
  - `headers` - `optional`, 用户可以自己定义headers, 用于处理请求时候验证
  - `secret` - `optional`, 用于对data的签名, 签名也同样会放到请求头中
  - `type` - sync / async

## Signature

系统可以对webhook的内容进行签名, 将签名的内容提交到请求的url, 以供客户可以验证内容是否有效, 签名内容放到请求头中, 使用secret对请求的内容采用hmac_sha1算法生成

`X-Hub-Signature: sha1=xxxxxxxxx`

## Webhook Event

webhook event统一采用 noun.verb 的形式来定义, noun 可以为某个实体, 也可以为某一个实体的子实体

  - chat.visitor.requested
  - chat.started
  - chat.ended
  - chat.visitor.replied
  - chat.visitor.submitted_survey
  - chat.visitor.uploaded_file ??
  - chat.agent.replied
  - chat.agent.wraped_up
  - agent.status.changed
  - offline_message.submitted
  - ticket.created
  - ticket.status.changed
  - ticket.closed
  - // visitor.location.sent
  - visitor.location.sent
  - bot.answer.rated ??
  - bot.message_received ??


## Webhook Request

```json
{
  "event": "chat.started",
  "unique_id": "guid-guid-guid-guid",
  "time": 138789898, 
  "data": {
    "chat":{
      "id": "xx",
      // other properties
    },
    "visitor": {
      "name": "",
      "email": "",
      // other properties
    }
  }
}
```

  - `event` - 表示event名字
  - `unique_id` - 表示这个事件的唯一id, 在server重试时接受方可以判断是否已经接收到了而直接丢掉



