
# General

1. [用户可通过定义下面形式的Manifest文件来将phone引入进来](#phone-manifest)
2. [用户可以通过manifest配置一个Agent Console端全局菜单及页面用来显示phone的功能及配置](#agentconsole-global-menu)
3. [用户可以通过manifest中的配置添加一个Agent Console NavigationBar](#agent-console-navigationBar)
4. [用户可以通过manifest中的配置添加一个Agent Console SideBar](#agent-console-mychats-sidebar)
5. [用户可以通过manifest中的配置添加一个通知区域图标](#agent-console-notification-area)
6. [用户可以通过Manifest中的配置添加一个配置页面](#phone-settings)
7. [用户可以通过Restful API获取Agent状态来判断是否分配Phone给当前Agent](#agnet-status)
8. [自定义通知](#custom-notification)
# 开放平台 Chat phone的接入点

## phone manifest

用户可通过定义下面形式的Manifest文件来将phone引入进来。


```json
{
  "name": "Comm100phone", //App的名字
  "author": {
    "name": "Comm100", //开发者的名字
    "email": "developer@comm100.com",  //开发者的邮箱
    "url": "https://www.comm100.com/phone" //开发者的主页地址或App的主页地址
  },
  "private": false,  //该APP是否是私有APP的标志
  "version": "1.0",
  "frameworkVersion": "2.0",
  "location": {
    "agentConsole": {
      "topbar": {       //顶部全局菜单
        "icon": "/assets/phone.ico",
        "url": "/topbar/index.html",
        "title": "phone",
        "tips": "Comm100 phone..."
      },
      "navigationBar":{    //左侧导航菜单
         "url": "navigationBar/list.html",
         "title": "liveCalls"
      },
      "chat_tabSide":{     //聊天页面的tab区域
        "icon": "/assets/phone.ico",
        "url": "/chat_tabSide/index.html",
        "title": "phone",
        "tips": "Comm100 phone..."
      },
      "notificationArea": {   //右下角通知区域
        "icon": "/assets/phone.ico",
        "title": "phone",
        "tips": "Comm100 phone..."
      }
    },
    "visitor": {
      "customCSS": [
        "/assets/visitor_custom_css.css"
      ],
      "customJS": [
        "/assets/visitor_custom_js.js"
      ],
    }
  },
  "settings":{
    "url": "/assets/phoneSettings.html"
  }
}
```

## AgentConsole Global Menu

用户可以在AgentConsole端配置一个全局菜单用来进行Phone的相关操作及配置, 可以在APP的manifest中可以声明这个菜单

```json
  {
    "location": {
      "agentConsole": {
        "topbar": {       //AgentConsole顶部全局菜单
          "icon": "/assets/phone.ico",
          "url": "/topbar/index.html",
          "title": "phone",
          "tips": "Comm100 phone..."
        }
      }
    }
  }
```

1. 安装APP以后, 会在AgentConsole的右上角区域显示phone的菜单, 点击phone进入到phone的操作及配置界面
2. 菜单点击展示的内容为一个div, div加载用户配置的这个页面`/topbar/index.html`
  - 具体的url可能为`https://hosted.comm100.com/apps/phone/index.html`
3. 该页面的权限配置及身份认证同LiveChat的Agentconsole上增加一个配置菜单

## Agent Console NavigationBar

用户可以通过manifest中的配置添加一个Agent Console NavigationBar, Agent Console的左侧导航菜单会添加title中菜单，点击该菜单会显示`/navigationBar/index.html`页面

```json
  {
    "location": {
      "chat": {
        "agent": {
          "navigationBar": {
            "url": "navigationBar/list.html",
            "title": "liveCalls"
          }
        }
      }
    }
  }
```

## Agent Console Mychats Sidebar

用户可以通过manifest中的配置添加一个Agent Console Mychats SideBar, Agent Console的Chats右侧菜单会显示`/sidebar/index.html`页面

```json
  {
    "location": {
      "chat": {
        "agent": {
          "chat_tabSide":{     //聊天页面的tab区域
            "icon": "/assets/phone.ico",
            "url": "/chat_tabSide/index.html",
            "title": "phone",
            "tips": "Comm100 phone..."
          }
        }
      }
    }
  }
```
## Agent Console Notification Area

用户可以通过manifest配置一个Agent Console Notification Area的图标, Agent Console在接收到该APP的通知的时候触发NotificationArea的图标闪动。

```json
  {
    "location": {
      "chat": {
        "agent": {
          "notificationArea": {   //右下角通知区域
            "icon": "/assets/phone.ico",
            "title": "phone",
            "tips": "Comm100 phone..."
          }
        }
      }
    }
  }
```

## phone settings

用户可以通过Manifest中的配置添加一个配置页面，来进行Phone的相关配置，如Agent与分机号的配置.

```json
  {
    "settings":{
      "url": "/assets/phoneSettings.html"
    }
  }
```

获取Agent列表：用户可以获取Agent列表的信息来配置Agent与电话的对应关系

1. Request

`GET 'https://hosted.comm100.com/livechatapi/operators'`

2. Response

```json
[ 
    {
        "api_key":"ef43f9362aac4f60ad428cb4d072f2c8", 
        "departments":[
            1,
            2
        ],
        "email":"allon@comm100.com",
        "id" : 1,
        "max_chats_count":5,
        "name":"allon",
        "ongoing_chats":2,
        "status":0
    },
    {
        "api_key":"5ce7c8010251448f91b7eedc7931c1e9", 
        "departments":[
            2,
            3
        ],
        "email":"roy@comm100.com",
        "id":2, 
        "max_chats_count":5,
        "name":"roy",
        "ongoing_chats":0,
        "status":1
    },
]
```

## Agent Status

获取Agent状态：用户可以获取当前的Agent信息中的phoneStatus的值，来是否对Agent分配phone

1. Request

`GET 'https://hosted.comm100.com/livechatapi/operators/{operator_id}'`

2. Response

```json
  { 
    "api_key":"ef43f9362aac4f60ad428cb4d072f2c8",
    "departments":[ 1, 2 ], 
    "description":"live chat support",
    "display_name":"kim",
    "email":"kim@comm100.com",
    "first_name":"kim",
    "id":1, 
    "is_active":"ture",
    "is_admin":"true",
    "last_name":"hu",
    "max_chats_count":5,
    "name":"kim",
    "ongoing_chats":0, 
    "phone":"15874261010",
    "status":0,
    "title":"support",
    "phoneStatus": "available"     //available/busy/away
  }
```
## Custom Notification

通知提醒：用户在接收到Phone的时候可以通过下面的API对当前的Agent进行通知提醒

Request

`POST 'https://hosted.comm100.com/livechatapi/operators/{operator_id}/notifications'`

```javascript
  // body struct
  const notification =  {
    siteId: 10000,
    operatorId: 28888,
    notificationLocation: {      
      notificationArea: true,    //右下角通知区域闪动，点击后激活AgentConsole
      navigationBar: true,       //左侧List菜单区域，点击后打开Manifest中配置的list页面
      topBar: true               //右上角全局菜单区域，点击后打开Manifest中配置的菜单页面
    }
  }

  // body struct   //按不同的通知位置进行类型区分，分开触发通知
  const notification =  {
    siteId: 10000,
    operatorId: 28888,
    notificationType: "notificationArea"   //notificationArea/navigationBar/topBar
  }
```

顶部菜单提醒方式：用户可以在AgentConsole收到顶部菜单提醒的时候设置提醒的方式和内容

```javascript
Comm100AgentConsoleAPI.on("agentconsole.notification.receive", function () {   //在收到提醒的时候
  Comm100AgentConsoleAPI.set("agentconsole.topbar.notificationType","div");    //div方式显示内容
  Comm100AgentConsoleAPI.set("agentconsole.topbar.isActive","true");           //自动弹出div内容
});
```
