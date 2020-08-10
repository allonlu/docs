# Chat Server Recover Chat
 
## visitor basic info
 
访客基本信息的恢复会在访客端恢复访客或者agent端恢复访客时恢复这些信息。
 

### 状态恢复策略说明
 
| Strategy | Description |
| - | - |
| Specify | 指定特定的值, 一般为时间 |
| Any | 接受任意一方的值, 只要Visitor存在就不会重新覆盖, 一般为访客不会变的属性 |
| visitor override agent | 以visitor的信息为优先, visitor会覆盖agent的信息 |
| agent override visitor | 以agent的信息为优先, agent会覆盖visitor的信息 |
| agent only | 表示这个数据只在agent端存在, 需要agent恢复 |
| vistior only | 表示这个数据只在visitor端存在, 需要visitor恢复 |
| none | 表示该信息不需要恢复 |
 
### 具体访客属性对应使用的策略
 
| Prop | Strategy | Description |
| - | - | - |
| latestActivityTime | specify - utc now | 访客最近活动时间 |
| latest time of waiting for chat | specify utc now | 访客在waiting for chat状态下的最近活动时间 |
| browser | any | 访客的浏览器 |
| ip | any | 访客访问的ip地址 |
| country | any | 访客所处的国家, from ip |
| city | any | 访客所处的城市, from ip |
| state | any | 访客所处的州, from ip |
| country code | any | 访客所处的国家码, from ip |
| flash version | any | 访客浏览器使用的flash版本 |
| language | any | 访客访问的语言 |
| operating system | any | 访客的操作系统 |
| screen resolution | any | 访客设备的屏幕分辨率 |
| first visit time | any | 访客第一次访问的时间 |
| enter site time | any | 访客进入站点的时间 |
| time zone | any | 访客所处的时区 |
| visit times | any | 访客的访问次数 |
| chat times | any | 访客的聊天次数 |
| landing page url/title | any | 访客的着陆页 |
| referrer url | any | 访客的引用页 |
| search engine | any | 访客使用的搜索引擎, from referrer url |
| keywords | any | 访客使用的搜索关键字, from referrer url |
| campaign id | visitor override agent | 访客的campaign id |
| auto invitation id | agent override visitor | 访客的auto invitation id, 请求聊天时传入 |
| department id | agent override visitor | 访客指定的部门 |
| custom variable | vistior override agent | 访客的变量, 按照每一个具体的变量覆盖 |
| custom fields | agent override visitor | 访客提交的prechat中的信息 |
| latest email | visitor override agent | 访客最近使用的email |
| latest name | visitor override agent | 访客最近使用的name |
| product service | visitor override agent | 访客提交prechat或者offline meessage中的信息 |
| company | visitor override agent | 访客提交prechat或者offline message中的信息 |
| email | visitor override agent | 访客提交prechat或者offline message中的信息 |
| name | visitor override agent | 访客提交prechat或者offline message中的信息 |
| phone | visitor override agent | 访客提交prechat或者offline message中的信息 |
| current page url/title | visitor override agent | 访客当前访问的页面 |
| current page visit time | visitor override agent | 访客访问当前页面的时间 |
| requesting page title / url | visitor override agent | 
| if captured | agent only | 访客是否被capture |
| capture time | agent only | 访客被capture的时间 |
| sf url | agent only | 访客的salesforce关联信息 |
| sf contact | agent only | 访客的salesforce关联信息 |
| sf account | agent only | 访客的salesforce关联信息 |
| sf lead | agent only | 访客的salesforce关联信息 |
| sf task | agent only | 访客的salesforce关联信息 |
| sf case | agent only | 访客的salesforce关联信息 |
| current sf object id | agent only | 访客的salesforce关联信息 |
| current sf object type | agent only | 访客的salesforce关联信息 |
| has edit filed in automatic mode | agent only | 访客的salesforce关联信息 |
| is need push | agent only | 访客是否需要推送, 仅手机访客端切换到后台时使用 |
| push config | agent only | 访客的推送配置, 仅手机访客端 |
| wrapup category list | agent only | wrapup信息 |
| operator comment | agent only | wrapup中的comment信息 |
| chat route target | agent only | 聊天路由目标 |
| avatar url | agent only | 访客的头像 |
| social media source | agent only | 访客从social media登录时的来源 |
| social media profile url | agent only  | 访客从social media登录时的profile地址 |
| zendesk url | agent only | 访客的zendesk地址 |
| segments | agent only | 访客的细分 |
| ticket id | agent only | 聊天关联的ticket id |
| if searching kb | visitor override agent | 访客当前是否正在查询kb |
| visited page urls | visitor only | 访客访问过的页面 |
| navigations | vistior only | 访客的浏览轨迹 |
| sso id | none | 访客的sso id, 因为有安全问题, 因此这个地方不恢复, 如果在bot中需要用到则会被判断为需要再次登录 |
| error message sf chat ended | none | salesforce集成过程中的错误信息 |
| location | none | 访客的位置信息 |

## visitor status
 
访客状态需要根据不同情况分成访客的状态以及聊天相关的状态， 访客的状态就直接根绝恢复时上传的访客状态来恢复， 聊天相关的状态需要根据聊天消息来恢复，具体见下放。
 
| status | strategy | description |
| - | - |
| in site | in site | in site 状态的访客就直接设置为这个状态即可 |
| pre chat | pre chat | pre chat状态也可以直接副这个状态，同时将latestPrechatTime设置为当前时间 |
| offline message | offline message | 直接设置为offline message状态，同时将latestOfflineMessageTime设置为当前时间 |
| manual invitation | manual invitation |
| auto invitation | auto invitation |
| refuse by visitor | refuse by visitor |
| system processing | waiting | 这个要当作system process完成以后来处理 |
| out of site | none  | |
 
## chat

聊天中的属性有根据实际情况有两种恢复方法， 一种是直接使用agent/visitor提交上来的信息来恢复， 另一种则是通过聊天消息来恢复，聊天的消息是通过所有的agent以及访客本地的消息合并以后来进行恢复的。

| Prop | Strategy |
| - | - |
| if enter queue | agent only |
| monitor agents | agent only |
| source | visitor override agent |
| visitor message seen by agent | agent override visitor | 
| agent message seen by visitor | visitor override agent |


### 基于动作的状态恢复
 
| Prop | Actions | Description |
| - | - |
| request time | visitor request chat | 聊天的请求时间 |
| start time | agent accept chat, bot start chat | 聊天开始时间 |
| transfer begin time | agent transfer department | 聊天转移开始时间 |
| time begin waiting | request chat, request chat to bot | 聊天开始等待的时间 |
| chat type | bot accept chat, agent accept chat | 聊天的类型， 
| current agents | agent accept chat, agent join chat, agent stop chat |
| assignee | agent add invitation text | 手动邀请时指定的agent |
| translation | agent enable translation, agent disable translation | 
| audio video chat | agent/visitor accpet/request/refuse/cancel audio/video chat |
| cobrowse | agent request cobrows ... |
| files | agent send file/image, agent sent file/image |

| manual invitation by window | agent add invitation text |
| refused by agent | agent refused chat |
| waiting for chat | visitor request chat |
| chating | visitor accept chat, agent accept chat |
| transferring | agent transfer to department, agent refuse tresfer, agent accept transfer |
| chat ended | system chat ended |


# Client

对于客户端来说，有写状态需要保留一段时间以保证最终状态一致

1. ChatEnded状态，访客端和Agent端都需要恢复一下聊天
  - 访客端要保持心跳一段时间, 3m以后停止heartbeat
  - agent可以在保持聊天窗口还在的情况下恢复聊天
2. 访客端处于Refused by Agent状态， 访客端需要恢复一下聊天
  - 访客端保持心跳一段时间, 3m以后停止heartbeat
3. Agent 恢复Refused by Agent状态，需要恢复一条refused的消息让访客端可以收到refused状态 - 在两端状态不一致的时候有问题（agent还处于waiting状态）
4. UnfinishedWrapup 需要单独通过agent端恢复
