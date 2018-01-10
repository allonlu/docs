
# General

1. 用户可以通过manifest配置一个顶级菜单及页面用来显示Bot的功能配置
2. 用户可以通过manifest中的配置添加一个Agent Console SideBar
3. 用户可以通过manifest中Bot的配置接入一个自己的Bot系统
4. 用户可以通过manifest中访客端的js配置,接入处理Bot消息的接口
5. Campaign中关于bot的配置在Live Chat中写死,暂时不提供开放性

# 开放平台 Chat Bot的接入点

## 用户可以配置一个顶级菜单及页面用来配置Bot的功能配置

APP的manifest中可以声明一个产品级菜单的功能

```json
  {
    "location": {
      "product": {
        "name": "Bot",
        "url": "/manage/index.html"
      }
    }
  }
```

1. 安装APP以后, 会在后台一级菜单上显示一个Bot的菜单, 点击Bot进入到配置界面
2. 菜单下面的内容为一个iframe, iframe加载用户配置的这个页面`/manage/index.html`
  - 具体的url可能为`https://hosted.comm100.com/apps/chat-bot/manage/index.html`
3. 该页面的权限配置及身份认证同LiveChat后台菜单上增加一个配置页面


## 用户可以通过manifest中的配置添加一个Agent Console SideBar

可以通过Agent Console的扩展来实现, 在manifest中配置以下内容, Agent Console的Chats右侧菜单会显示`/sidebar/index.html`页面

```json
  {
    "location": {
      "agentConsole.chats.sideBar": {
        "icon": "/assets/bot.ico",
        "url": "/sidebar/index.html",
        "title": "ChatBot",
        "tips": "Comm100 ChatBot..."
      }
    }
  }
```

## 用户可以通过manifest中Bot的配置接入一个自己的Bot系统

```json
  {
    "bot": {
      "endpoint": {
        "list": {
          "method": "GET",
          "url": "https://api.chatbot.com/comm100/{siteId}/bots",
          "headers": [
            "Authorization": "Bearer {app.metadata.token}",
            "x-api-version": "v2",
          ]
        },
        "message": {
          "method": "POST", //必须为post
          "url": "https://api.chatbot.com/comm100/{siteId}/bots/{id}/message",
          "headers": [
            "Authorization": "Bearer {app.metadata.token}",
            "x-api-version": "v2",
          ]
        }
      }
    }
  }
```

### List

用户加载campaign >> bot页面时请求对应的接口获取具体的bot列表

1. Request

  `GET 'https://api.chatbot.com/comm100/10000/bots'`

2. Response

  ```json
    [
      {
        "id": "i934ru90ruoewq",
        "name": "Bot 1",
        "avatar": "https://cdn.chatbot.com/avatar/ig923udjfoew89.png",
        "greetingMessage": "Hello {visitor.name}, I'm ChatBot. What can I do for you?",
        "language": "en_us"
      }
    ]
  ```

### Message

Chat Server收到visitor的消息时会调用message接口

1. Request

  `POST 'https://api.chatbot.com/comm100/10000/bots/i934ru90ruoewq/message'`
  ```javascript
    // body struct
    const botMessage =  {
      siteId: 10000,
      botId: 'i934ru90ruoewq',
      chatId: 28888,
      visitor: {
        id: 77777777,
        name: 'Allon',
        email: 'allon.lu@comm100.com',
        phone: '18888888888',
        ssoId: ''
        customFields: [
          {
            name: 'product',
            value: 'livechat,ticket',
          }
        ],
        customVariable: [
          {
            name: 'age',
            value: '88',
          }
        ],
      }
      message: {
        type: 'text',  // text/image/file/custom
        content: 'I have submit this form.',
        extend: { // 动态对象, custom类型用户可以设置任意的属性和值, 这个对象是由访客端提交的时候定义的, 在bot engine中处理
          // ...
        },
      }
    }
  ```

2. Response
  ```javascript
    const responseMessage = {
      type: 'text', // text/image/file/custom
      content: 'Your balance is $99.00', // 聊天记录会保存这条消息
      extend: { // 动态对象, 针对custom类型用户可以设置任意的属性和值, 在访客端前端处理这个对象
        // type: 'form', // require_login/form/high_confidence_answer/possible_answer/no_answer
        // ...
      }
    }
  ```

3. System Extend Object
  ```javascript
    const imageExtend = {
      name: "id.png",
      url: "https://chatserver.comm100.com/download.ashx?guid=jiqerjoi23jrasdf",
    }
    const fileExtend = {
      name: "id.png",
      url: "https://chatserver.comm100.com/download.ashx?guid=jiqerjoi23jrasdf",
    }
  ```


## 用户可以通过manifest中访客端的js配置,接入处理Bot消息的接口

### manifest 中的定义

```json
  {
    "visitor": {
      "customJS": [
        "/assets/visitor_custom_js.js"
      ]
    }
  }
```

安装这个APP以后, 前端在加载访客端的js以后会加载上面的js

### 访客端Custom JS接口

1. 访客端处理Bot的消息

  ```javascript
    Comm100API.render('livechat.chat.message.custom', (message) => {

      // return custom HTMLElement to replace default component
      // const customComponent = document.createElement('div');
      // return customComponent;

      if (message.extend && message.extend.type === 'form') {
        // return <form >
        // return <iframe url='message.extend.url' >
      }
      else if (message.extend && message.extend.type === 'high_confidence_anwser') {
        //return <high >
      }
    });
  ```

2. 访客端提交Form

  ```javascript
    const message = {
      type: 'custom',
      content: 'I submit a form. \n name: allon \n email: allon.lu@comm100.com',  // 聊天记录中会保存这条消息
      extend: { // 动态结构, 开发者可以任意定义这个结构, ChatBot那边会接收到这部分信息
        type: 'form_submit',
        data: {
          name: 'allon',
          email: 'allon.lu@comm100.com',
        }
      }
    };
    Comm100API.do('livechat.chat.message.send', message);
  ```


