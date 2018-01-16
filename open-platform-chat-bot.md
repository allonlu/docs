
# General

1. [用户可通过定义下面形式的Manifest文件来将自己的Bot引入进来](#bot-manifest)
2. [用户可以通过manifest配置一个顶级菜单及页面用来显示Bot的功能配置](#product-menu)
3. [用户可以通过manifest中的配置添加一个Agent Console SideBar](#agent-console-sidebar)
4. [用户可以通过manifest中Bot的配置接入一个自己的Bot系统](#external-bot)
5. [用户可以通过manifest中访客端的js配置,接入处理Bot消息的接口](#visitor-side-handle-bot-message)
6. [Campaign中关于bot的配置在Live Chat中写死,暂时不提供开放性](#campaign-bot)
7. [Manifest中可用宏定义](#micro-define)
# 开放平台 Chat Bot的接入点

## Bot manifest

用户可通过定义下面形式的Manifest文件来将自己的Bot引入进来。


```json
{
  "name": "Comm100Bot", //App的名字
    "author": {
      "name": "Comm100", //开发者的名字
      "email": "developer@comm100.com",  //开发者的邮箱
      "url": "https://www.comm100.com/bot" //开发者的主页地址或App的主页地址
    },
  "private": false,  //该APP是否是私有APP的标志
  "version": "1.0",
  "frameworkVersion": "2.0",
  "location": {
    "product": {
      "name": "Bot",
      "url": "/manage/index.html"
    },
    "chat": {
      "agent": {
        "chatsSidebar": {
          "icon": "/assets/bot.ico",
          "url": "/sidebar/index.html",
          "title": "ChatBot",
          "tips": "Comm100 ChatBot..."
        },
      },
      "visitor": {
        "customCSS": [
          "/assets/visitor_custom_css.css"
        ],
        "customJS": [
          "/assets/visitor_custom_js.js"
        ],
      },
      "bot": {
        "endpoint": {
          "list": {     //机器人列表Endpoint配置
            "method": "POST",
            "url": "https://api.chatbot.com/comm100/{siteId}/bots", // {siteId}为宏, Comm100在处理这个地址时会替换掉这个参数
            "headers": [
              "Authorization": "Bearer {app.metadata.token}", // {app.metadata.token} 为后台安装这个app时设置的元数据, 
              "x-api-version": "v2",
            ]
          },
          "message": {   //机器人列表回答问题Endpoint配置
            "method": "POST", //必须为post
            "url": "https://api.chatbot.com/comm100/{siteId}/bots/{botId}/message", // {botId}为宏参，表示使用的bot Id, 用户可以选择设置或者不设置, body中的内容也会有botId的属性
            "headers": [
              "Authorization": "Bearer {app.metadata.token}", // {app.metadata.token} 为后台安装这个app时设置的元数据, 
              "x-api-version": "v2",
            ]
          }
        }
      }
    }
  },
}
```

## Product Menu

用户可以配置一个顶级菜单及页面用来配置Bot的功能配置, 可以在APP的manifest中可以声明一个产品级菜单的功能

```json
  {
    "location": {
      "product": {
        "name": "Bot",
        "title": "Bot",
        "url": "/manage/index.html"
      }
    }
  }
```

1. 安装APP以后, 会在后台一级菜单上显示一个Bot的菜单, 点击Bot进入到配置界面
2. 菜单下面的内容为一个iframe, iframe加载用户配置的这个页面`/manage/index.html`
  - 具体的url可能为`https://hosted.comm100.com/apps/chat-bot/manage/index.html`
3. 该页面的权限配置及身份认证同LiveChat后台菜单上增加一个配置页面

## Agent Console Sidebar

用户可以通过manifest中的配置添加一个Agent Console SideBar, Agent Console的Chats右侧菜单会显示`/sidebar/index.html`页面

```json
  {
    "location": {
      "chat": {
        "agent": {
          "chats_sidebar": {
            "icon": "/assets/bot.ico",
            "url": "/sidebar/index.html",
            "title": "ChatBot",
            "tips": "Comm100 ChatBot..."
          }
        }
      }
    }
  }
```

## External Bot

用户可以通过manifest中Bot的配置接入一个自己的Bot系统, 这个bot会参与到聊天的分配，并且在特定的时候会调用bot提供的两个接口

```json
  {
    "location": {
      "chat": {
        "bot": {
          "endpoint": {
            "list": {
              "method": "GET",
              "url": "https://api.chatbot.com/comm100/{siteId}/bots", // {siteId}为宏, Comm100在处理这个地址时会替换掉这个参数
              "headers": [  //  用户可以自定义请求头
                "Authorization": "Bearer {app.metadata.token}", // {app.metadata.token} 为后台安装这个app时设置的元数据, 
                "x-api-version": "v2",
              ]
            },
            "message": {
              "method": "POST", //必须为post
              "url": "https://api.chatbot.com/comm100/{siteId}/bots/{botId}/message", // {botId}为宏参，表示使用的bot Id, 用户可以选择设置或者不设置, body中的内容也会有botId的属性
              "headers": [
                "Authorization": "Bearer {app.metadata.token}",
                "x-api-version": "v2",
              ]
            }
          }
        }
      }
    }
  }
```

### Bot List

用户加载campaign >> bot页面时请求对应的接口获取具体的bot列表, 另外Chat Server将聊天分配到具体的bot时，需要从这个接口重新获取列表，检查列表中的具体id的bot, 读取name, avatar, greetingMessage等信息用作具体的聊天 

  1. Request

  `GET 'https://api.chatbot.com/comm100/10000/bots'`

  2. Response

  ```json
    [
      {
        "id": "@bot_id",
        "name": "Bot 1",
        "avatar": "https://cdn.chatbot.com/avatar/ig923udjfoew89.png",
        "greetingMessage": "Hello {visitor.name}, I'm ChatBot. What can I do for you?",
        "language": "en_us"
      }
    ]
  ```

### Bot Engine Handle Visitor Message

Chat Server收到visitor的消息时会调用message接口

  1. Request

  `POST 'https://api.chatbot.com/comm100/10000/bots/i934ru90ruoewq/message'`

  ```javascript
    // body struct
    const botMessage =  {
      siteId: 10000,
      botId: '@bot_id',
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
      content: 'Your balance is $99.00',
      extend: { // 动态对象, 针对custom类型用户可以设置任意的属性和值, 在访客端前端处理这个对象
        // type: 'form', // require_login/form/high_confidence_answer/possible_answer/no_answer
        // ...
      }
    }
  ```

  3 Custom Message Extend 

  Form Extend

  ```javascript
    const formAnswer = {
      type: 'custom',
      content: '[Booking Room]',
      extend: {
        type: 'high_confidence_answer',
        intent: {
          id: '@intent_id',
          responseType: 'form',
          title: 'Order',
          loginRequired: false,
          form: {
            fields: [
              {
                type: 'text',
                name: 'email',
                required: true,
                value: 'allon@comm100.com',
              },
              {
                type: 'text',
                name: 'address',
                required: false,
                value: 'Hangzhou',
              },
              {
                type: 'checkbox',
                name: 'useSSL',
              }
            ]
          }
        }
      }
    }
  ```

  Message Anwser
  
  ```javascript
    const imageAnswer = {
      type: 'custom',
      content: 'This is a high confidence answer!',
      extend: {
        type: 'high_confidence_answer',
        intent: {
          id: '@intent_id',
          responseType: 'message',
          response: 'This is a high confidence answer!.',
        }
      }
    }
  ```

  Possible Answer
  
  ```javascript
    const possibleAnswer = {
      type: 'custom',
      content: 'I\'ve got you some relevant questions that may help. ...'
      extend: {
        type: 'possible_answer',
        intents: [
          {
            id: '@intent_id',
            name: 'install code',
          },
          {
            id: '@intent_id',
            name: 'install agent console',
          }
        ]
      }
    }
  ```

  No Answer

  ```javascript
    const noAnswer = {
      type: 'custom',
      content: 'Hi, I am still learning. I am afraid ...',
      extend: {
        type: 'no_answer',
        chatWithAgent: true,
      }
    }
  ```

## Visitor Side Handle Bot Message

用户可以通过manifest中访客端的js配置,接入处理Bot消息的接口

```json
  {
    "location": {
      "chat": {
        "visitor": {
          "customJS": [
            "/assets/visitor_custom_js.js"
          ]
        }
      }
    }
  }
```

安装这个APP以后, 前端在加载访客端的js以后会加载上面的js

### JS API

```javascript

  Comm100API.on('livechat.chat.message.received', (message) => {
    if (message.type === 'custom') {
      const messages = Comm100API.get('livechat.chat.message');
      const newMessags = messages.update((msg) => {
        if (message.id === msg) 
          return Object.assign({}, msg, { render: renderCustom })
        else
          return msg;
      });
      Comm100.API.set('livechat.chat.message', messages);
    }
  });

  const bot = Comm100API.get('livechat.chat.bot'); // 获取当前聊天的bot信息

  const loginConfiguration = await axios.get(`https://bot.comm100.com/bot/${bot.id}/login`); // 获取当前bot的登录配置

  const checkIfLogined = () => {
    const authVariable = loginConfiguration.authVariable;
    const authVariableValue = Comm100API.get('livechat.customVariable').find((v) => v.name === authVariable));
    if (authVairableValue && authVairableValue.value !== null && authVairableValue.value != '') {
      return true;
    }
    return false;
  }

  const loginWithVairable = (variable) => {
    Comm100API.set('livechat.customVariable', {variable});
  }

  const showLogin = () => {
    if (loginConfiguration.method === 'sso') {
      // show sso login 
    } else if (loginConfiguration.method === 'extended_window'){
      const loginTab = {
        id: 'login_tab',
        isSystem: false,
        content: function () {
          const iframe = document.createElement('iframe');
          iframe.url = loginConfiguration.loginUrl;
          // iframe should set the login variable
          return ifrmae;
        }
      }
      Comm100API.set('livechat.extendedWindow.tabs', [loginTab]);
      Comm100API.set('livechat.extendedWindow.isVisible', true);
    } else {
      // other method
    }
  }

  const renderCustom = (message) => {
    if (message.extend && message.extend.type === 'high_confidence_anwser') {
      if (message.extend.intent.responseType === 'form') {
        if (message.extend.intent.loginRequired) {
          if (!checkIfLogined()) {
            // return user should login first information and login button/link.
            // <a click='showLogin()'>login</a>
          }
        }
        if (message.extend.intent.form.disabled) {
          // return disabled form
        } else if (message.extend.intent.form.submitted){
          // return <form > or <iframe url='message.extend.url' > with modal 
        } else {
          // return normal form
        }
      } else if (message.extend.intent.responseType === 'message') {
        // return the message content
      }
    } else if (message.extend && message.extend.type === 'possible_answer') {
      // return possible answer content
    }
  }

  // 将form置为disable, 其实质就是修改form中的数据
  const disableForm = (messageId) => {
    const messages = Comm100API.get('livechat.chat.message');
    const newMessages = messages.update((msg) => {
      if (msg.id === messageId) {
        // set message.extend.form.disabled = true;
        // return msg
      }
      return msg;
    })
    Comm100API.get('livechat.chat.message', newMessages);
  }

  const selectIntent = (intentId, message) => {
    const message = {
      type: 'custom',
      content: 'Install code',
      extend: {
        type: 'select_intent',
        intentId: '@intent_id',
      }
    }

    Comm100API.do('livechat.chat.message.send', message);
  }

  const setAnswerHelpful = (helpful, messageId, intentId) => {
    // 更新状态, 将这个回复标记为helpful or not
    updateMessageHelpful(messageId, helpful);
  
    // 提交这个操作到bot server
    submitAnswerHelpful(intentId, helpful);
  }

  // 访客提交form
  const submitForm = (messageId, intentId, fields) => {
    // 更新状态, 将form置为已提交
    updateMessageFormSubmitted(messageId);

    // 组织form的内容
    const message = {
      type: 'custom',
      content: 'I submit a form. \n name: allon \n email: allon.lu@comm100.com',
      extend: {
        type: 'form_submit',
        messageId: '@message_id',
        intentId: '@intent_id',
        data: {
          name: 'allon',
          email: 'allon.lu@comm100.com',
        }
      } 
    }

    // 将Form提交到chat server
    Comm100API.do('livechat.chat.message.send', message);
  }
```

## Campaign Bot

Campaign中关于Bot中的配置目前不开放设置，Campaign对应bot的关系由Comm100维护, 列表由上面的List接口获取, 从列表中获取到的信息会列在Comm100本身bot的后面, 其他关于分配的逻辑由Comm100定死, 用户只能配置公开的两个选项。

## Micro Define

Manifest中可用的宏如下所示：

```javascript
    const site = {
      id: '10000',            // {site.id}
      name: 'comm100',        // {site.name}
    }

    const chat = {
      id: '@chat_id',     // {chat.id}
      campaignId: '@campaign_id', // {chat.campaignId}
      visitor: {    
        id: '@visitor_id',    // {chat.visitor.id}
        name: 'Allon',        // {chat.visitor.name}
        email: 'allon.lu@comm100.com',  // {chat.visitor.email}
      },
      bot: {
        id: '@bot_id',      // {chat.bot.id}
        language: 'en_us',  // {chat.bot.language}
      }
    }

    const app = {
        app:{
          metadata:{ 
            token: '@token'   //{app.metadata.token}
          }
        }      
    }

    const agent = {
      agent:{
        metadata:{ 
          token: '@token',  // {agent.metadata.token}
          email: 'allon.lu@comm100.com',  // {agent.metadata.email}
        }
      }      
    }
```