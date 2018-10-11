# 定义资源的JSON结构

针对一个资源, 首先定义资源的属性, 针对该资源的增删改查都是基于这个资源的属性来控制参数和返回, 资源属性的标准格式如下: 

  | Name | Type | Read-only | Mandatory | Description |
  | - | - | :-: | :-: | - |
  | `id` | integer | yes | no | id of this agent |
  | `name` | string | no | yes | name of this agent |
  | `status` | string | no | no | type of this agent, maybe `online`, `away` or `offline` |
  | `departments` | integer[]  | no | no | department ids of this agent belong |
  | `permissions` | [permissions](#permissions) | no | no | permission of this group |
  | `preference.isEnableAutoTranslation` | string | no | no | whether enable auto translation for this agent |
  | `preference.autoTranslationLanguage` | string | no | no | the language of this agent uses auto translation |

  + 列表属性说明:
    - `Name` - 表示字段的名字, 如果名字中有`.`结构, 则表示这是一个子结构, 在该子结构只在这里使用是应该用这种方式定义
    - `Type` - 表示该字段的类型, 可以为常规的数据类型也可以为另外一个结构(如果是另一个结构应该加上链接), 如果类型后面有`[]`表示该字段是一个数组
    - `Read-only` - 表示该字段是否只读, 如果只读则表示用户无法通过新增/编辑指定该字段的值
    - `Mandatory` - 表示该字段是否必须, 如果必须则表示用户在新增时必须提供该字段的值
    - `Description` - 表示该字段的说明, 描述可能的值, 如果是枚举类型则需要列出所有可能的值, 如果有默认值还需要描述默认值


  如上述表格中定义的Agent的JSON结果如下
  ```json
  {
    "id": 1,
    "name": "allon",
    "status": "online",
    "departments": [
      1,
      2
    ],
    "permissions": {
      "acceptChat": true,
      "monitorChat": true
    },
    "preference": {
      "isEnableAutoTranslation": true,
      "autoTranslationLanguage": "en",
    }
  }
  ```

# 针对需要的操作功能定义资源的endpoint以及method

## HEAD
  
  在实际使用中, 会使用到唯一性检查, 采用HEAD操作来获取, 返回404就表示不存在, 否则就是存在的, 如果存在可以返回200, 顺便带上这个资源的endpoint

  - 如判断名字为LiveChat的Department是否存在
  
    `HEAD api/v2/livechat/departments/names/LiveChat`
  
      ```json
      {
        "url": "https://hosted.comm100.com/api/v2/livechat/departments/1"
      }
      ```

## GET
1. 获取批量的数据, 采用GET方法获取, 参数通过queryString传递
  
  如获取5月份的聊天数据:
  `GET api/v2/livechat/chats?timeFrom=2018-05-01&timeEnd=2018-05-31`

2. 获取单个资源, 采用GET方式获取, 一般使用id定位资源
  
  如获取id为1的聊天数据
  `GET api/v2/livechat/chats/1`

3. 获取资源下面的子资源, 采用GET方式, 使用id及子资源的名称来定位资源

  - 如获取id为1的campaign下面的prechat信息:

    `GET api/v2/livechat/campaigns/1/prechat`

  - 如获取id为1的category下面的canned messages:

    `GET api/v2/livechat/categories/1/cannedMessages`

    注: 针对这种存在层次关系的可以同时提供两种API, 一种为下面采用query的方式, 一种为上面描述的层级关系

      `GET api/v2/livechat/cannedMessages?categoryId=1`

## POST 
1. 提交新的资源, 采用POST方式新增资源, 参数通过JSON格式传递, 结构中描述的Mandatory为yes的属性必须有值, Read-Only的属性则不需要传
  
  - 如新增一个Campaign:

    `POST api/v2/livechat/campaigns`

      ```json
      {
        "name": "support",
        "language": "en",
      }
      ```

2. 批量更新

  指定所有的

3. 其他操作

  - Merge一个Ticket到另一个ticket

## PUT

  当需要完整更新一个资源时, 使用PUT方法, 参数为需要更新的JSON格式对象.
  
  如更新id为1的camapgin:
  `PUT api/v2/livechat/campaigns/1`
  ```json
  {
    "id": 1,
    "name": "for website",
    "language": "en",
  }
  ```

## DELETE

  当需要删除一个资源时, 使用DELETE方法

  如删除id为1的Campaign:
  `DELETE api/v2/livechat/campaigns/1`

## PATCH
  
  当需要部分更新一个资源时, 使用PATCH方法, 参数为需要更新的对象中的部分属性组成的JSON格式
  
  如只需要更新id为1的Campaign的语言为中文: 
  `PUT api/v2/livechat/campaigns/{id}`
  ```json
  {
    "language": "zh",
  }
  ```


# 描述输入输出
