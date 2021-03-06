# General
1. [业务目标](#business-goals)
2. [角色定义](#role-definition)
3. [业务场景](#business-scenario)
3. [需求分析](#requirement-analysis)
4. [设计与实现](#design-implementation)

## Business Goals
1. 企业客户成为Comm100的代理商，通过简单的配置，将Comm100产品直接销售给他的客户。
2. 企业客户利用Comm100的开放性，将Comm100的产品集中到自己的产品中，丰富自己的功能，加强自己产品的黏性。
3. Comm100借助合作伙伴的产品、销售等能力，与合作伙伴进行联合销售，实现双赢。

## Role Definition
 - Comm100 
 - Partner - 合作伙伴
   + 代理商 - 作为Comm100的代理商，销售Comm100的产品的企业
   + 集成商 - 需要将Comm100产品集成到自己产品中的企业
 - 客户 - 合作伙伴的客户，每个客户有自己的站点
 - Agent - 客服，隶属于合作伙伴的客户

## Business Scenario
1. Partner自定义一套Comm100的产品，直接将这个自定义产品卖给他的客户，供其使用。  
2. Partner将Comm100产品的功能集成到自己的系统中，提供给他的客户使用。

## Requirement Analysis

### Partner申请   
  企业可以通过Comm100提供的入口申请成为一个Partner，申请的过程中需要提供企业的名字、联系人及其电话号码、邮箱及其他一些必要信息，申请提交后由Comm100进行审核，审核通过以后Comm100会给企业生成一个Parter账号，该账号可以用来登录Partner应用系统。Comm100对于Partner的审核过程，包括资质审核、与Partner沟通使用说明、合作方式等。

### Partner管理系统及功能描述
  Comm100为Partner提供管理平台, 这是一个单独的系统, 只供Partner去使用,  可以管理Partner的配置信息以及Partner下面的所有站点信息。

  1. 登录 - Partner可以使用Parnter账号信息登录Partner管理, 每个Partner账号暂只提供一个账号, 不提供权限管理和控制
    - `email` - 登录Partner管理系统的username
    - `password` - 登录Partner管理系统的password

  2. Partner基本信息 - Partner可以修改自己的基本信息，包括联系人信息、公司信息等。
    - `company` - Partner的公司名称
    - `website` - Partner的公司网站
    - `description` - Partner的描述
    - `contact.name` - Partner的联系人名字
    - `contact.email` - Partner的联系人邮箱
    - `contact.phone` - Partner的联系人电话
    - `contact.title` - Partner的联系人title
    - `isActive` - 该Partner是否已激活, 由Comm100来控制数据库中的该值, Partner不能修改
  
  3. Partner配置Comm100产品品牌信息, 这些品牌信息会显示在用户的的Portal前端
    - `branding` - Comm100产品品牌名字
    - `productName` - LiveChat产品名字
    - `logo` - 用户可以设置Comm100产品的logo, 显示在系统的后台
      - `favicon` - 显示在后台页面头部的favicon
      - `logo` - 显示在后台头上的logo
  
  4. Partner可以配置前端样式
    - Phase I: 在目前的产品框架下，Comm100需要根据客户的需求定义前端样式文件, 配置到数据库中, 这个配置和定义不对Partner开放, 原则上只接受简单的样式定制, 如主题颜色的修改
      - 因为当前的产品的样式不是统一的, 后台的各个模块有部分样式都是独立的, 并且Live Chat 和Account的样式都是独立维护的, 而且这些样式跟Agent的样式又是不一样的, 所以目前无法公开这部分样式让用户自行修改, 如果单从这个层面去将样式统一工作量会比较大, 而且接下去前端改版时会做样式的统一, 所以在前端改版之前如果Partner需要定制样式就需要付费由Comm100来统一修改。
    - Phase II: Comm100统一所有的前端样式, 开放所有的前端样式给用户, 可以让用户自定义样式, 覆盖Comm100默认的样式

  5. Partner管理客户站点
    - 开户: Partner可以通过开户功能手动为客户建站, 开户需要输入以下信息
      - `email`
      - `password`
      - `lastName`
      - `firstName`
      - `company`
      - `website`
      - `country`
      - `phone`
    
    - 站点列表： 显示当前客户的站点信息
      - `id` - 站点Id
      - `company` - 站点的公司名字
      - `country` - 站点客户的国家
      - `agents` - 站点中的所有Agent, 显示Agent的Email
      - `maxAgents` - 站点最大Agent数量
      - `contact` - 站点联系人信息
      - `createTime` - 站点创建的时间
      - `status` - 站点的状态, 为 `trial` 或者 `paying`
        - `paying` - 针对试用期的站点可以点击转成付费账号, 如果是付费账号则不能转成试用期账号
      - `activated` - 站点是否已经激活
        - `active` - 操作, 点击可以激活站点
      - `disabled` - 站点是否已经激活
        - `disable/enable` - 操作, 点击可以禁用或者启用站点
    
  6. Partner管理安全相关配置
      - `Partner API IP white list` - 使用Comm100的Partner API和Partner应用系统的IP白名单, 可以配置多条
      - `Iframe Allow Domain` - Partner配置允许使用iframe调用的域名, 只有在配置列表中的域名会被允许, 这个会在页面加载时从http 头 `X-Frame-Options`返回
      - `Oauth Client` - Partner可以自己生成Oauth Client, 用作Comm100 RESTful API的Oauth验证, 每个Parnter只有一个Oauth Client
        - `client_id` - Oauth中的`client_id`, 在请求获取access_token时需要使用, 在添加Oauth Client时自动生成
        - `client_secret`- Oauth的`client_secret`, 在请求获取access_token时使用，在添加Oauth Client时自动生成
          - `reset_client` - Partner可以操作重置`client_secret`
        - `redirect_uris` - Oauth授权的回调接口, 可以配置多个, 以逗号隔开
        - `creation_date` - Oauth Client创建的时间
   

  7. Site 安全相关配置
    - 登录方式: Partner可以配置其下所有的站点是使用Comm100的登录还是使用SSO登录
    - `Partner Agent SSO` - Partner可以配置SSO, 配置以后该Partner下面的所有站点的Agent都会使用这个SSO来进行登录。
      - [SSO Settings](#sso-settings) -SSO配置
    

  8. Partner可以将Comm100产品集成到自己的系统中
    + 界面集成  
      - Partner可以直接使用链接或者iframe引入Live Chat后台管理界面到自己的产品中
      - Partner可以直接使用链接或者iframe引入Agent Console到自己的产品中
      - Partner可以在引入界面时去掉头， 脚，菜单
      - Partner可以使用自己的账号认证系统来通过认证, 认证完成后可以直接使用Comm100产品
      - Partner可以定义特定的前端样式以符合自己的产品视觉设计
    + API集成   
      - Partner可以通过API管理站点的Agent信息, 权限
      - Partner可以通过API管理Campaign, Canned Message等配置信息
      - Partner可以通过API获取Report数据, 集成在统一的Report模块
      - Partner可以通过API控制Agent的状态, 操作Chat

#### SSO Settings
- SAML
  + `cert` -SAML Certificate
  + `loginUrl`   
      SAML方式配置的登录页面，Agent在未登录的Comm100系统访问受限资源时会跳转到该地址, 并且将SAMLRequest提交到这个页面. 如：`https://partnerCompany.com/saml/sso`
  + `logoutUrl`   
      SAML方式配置的登出页面，在Agent点击登出时(agent console/portal), 会在Comm100这边将Session清除, 然后跳到该地址, 让Partner那边清除会话. 如：`https://partnerCompany.com/logout`,
- JWT
  + `loginUrl`   
    JWT方式配置的登录页面，Agent未登录时访问受限资源会跳转到该界面, 让用户去登录, 如：`https://partnerCompany.com/login`, 
  + `logoutUrl`   
      JWT方式配置的登录出面，同SAML的`logoutUrl`
  + `JWT Secret` - 系统自动生成, Partner可以Reset, 用作JWT的签名密钥
  + `other instructions`
    * `alg` Comm100只支持HS256的加密算法
    * `signature` jwt的签名密钥使用上面的`JWT Secret`


### 安装部署: Comm100可以根据Partner的需求来选择系统的部署方案：
   + Partner公共平台 -Partner平台使用二级域名来区分不同的Partner ，如`cisco.comm100.com`、`avaya.comm100.com`。
   + 单独部署 - Partner使用自己的独立域名，如`chat.cisco.com`。
   + 根据用户提供的信息编译特定的Agent Console Desktop, iOS, Android的客户端
    - 不同的Partner的客户端不能登录到其他Partner下面的站点

### 收费方案 
  - 每个Partner会根据情况配置好一个Plan, 不允许升级/切换, 允许试用, 如果在试用未转为正式账号, 在试用期结束以后将不能使用
  - Partner下面的站点由Partner自己向Partner收费, Comm100每月向Partner收取座席费用

# Partner API
  Partner API 主要是供Partner使用, Partner可以使用这些API为他的客户开启Comm100的账号, 维护这些站点.

  Partner API不支持跨域请求， 即在跨域的情况下只允许后台调用，在跨域情况下不能在浏览器中使用.

## Credential
  在调用Partner API之前, 开发者需要使用Partner的登录信息获取到`accesss_token`, 因为这个代码是Partner自己写的, 而且访问的数据也是Partner自己的数据, 所以可以采用Oauth中最简单的`Resource Owner Password Credentials Flow`来获取Token, 这个token只能访问到这个Partner自己的数据

  `GET https://partner.comm100.com/oauth/token`

  Request Parameters:
  - `grant_type` - `password`, 指定获取token的方式为password
  - `email` - partner email
  - `password` - partner password

  Response示例：
  ```json
    // Status: 200 OK
    {
      "access_token":"98asjdfka172dsfsd9s2342sdfs",
      "expires_in": "3600"
    }
  ```
  开发者可以通过上面获取的`access_token`来进行API调用，格式如下：

  `Authorization": "bearer {access_token}"`

## API endpoint

  Partner API中Partner可以使用的API如下：

  - `GET /api/v1/partner/sites/{site_id}` - 获取当前Partner的某一个站点的信息   
  - `GET /api/v1/partner/sites` - 获取当前Partner的所有客户的站点信息   
  - `POST /api/v1/partner/sites` - 开户，新建一个站点   
  - `PUT /api/v1/partner/sites/{site_id}` - 更新Partner的某一个客户的站点信息
  - `PUT /api/v1/partner/sites/{site_id}/paying` - 将试用期账号转为正式账号

### Get a Site

  `Get /api/v1/partner/sites/{site_id}`

  + Parameters

    - `site_id` - `required` 站点的Id 
  
  + Return
  
    返回指定站点编号的Site对象

      ```json
      {
        "id": 100001,     // 站点编号
        "company": "comm100", // 公司名字
        "website": "https://www.comm100.com", // 公司的网站地址
        "agents": 20, // 座席数
        "country": "China", // 国家
        "timezone": "GMT+08:00",  // 站点默认的时区
        "contact": {  // 站点的联系人信息, 一般为注册人的信息
          "firstName": "allon", // 联系人的名
          "lastName": "lu",     // 联系人的姓
          "email": "allon.lu@comm100.com",  // 联系人的邮箱地址
          "phone": "+08618888888888",       // 联系人的电话号码
        },
        "activated": true, // 站点是否已激活
        "disabled": false, // 站点是否被禁用
        "status" : "trial", // trial表示站点正在试用期, 如果是付费则是paying
      }
      ```

### Get Sites

  `GET /api/v1/partner/sites?keywords=comm100`

  + Parameters

    - `keywords` - `optional` 检索site的company/website, site contact的firstname/lastname/email, agent的name/email
    - `pageIndex` - `optional` 需要返回的索引页码


  + Return

    返回最多100个的Site对象列表及分页的信息
      
      ```json
      {
        "total": 888, // 符合当前查询的所有site数
        "previousPage" : "",  // 查询上一页的地址
        "nextPage": "", // 查询上一页的地址
        "sites" : [],   // 站点列表
      }
      ```

### Create Site

  `POST /api/v1/partner/sites`

  + Parameters `JSON`

    - `email` - `required` 注册站点的用户email, 也为注册站点contact的email
    - `password` - `required` 注册站点的用户password
    - `firstName` - `required` 注册站点的用户first name, 也为注册站点contact的first name
    - `lastName` - `required` 注册站点的用户last name, 也为注册站点contact的last name
    - `phone` - `optional` 注册站点的用户phone, 也为注册站点contact的phone number
    - `company` - `optional` 注册站点的company
    - `website` - `optional` 注册站点的website
    - `country` - `optional` 注册站点的country
    - `timezone` - `optional` 注册站点的默认时区

  + Reture

    返回创建的Site对象


### Update a Site

  `PUT /api/v1/partner/sites/{site_id}`

  + Parameters

    - `site_id` - `required` 站点的id

    + `JSON Data`

      - `activated` - `optional` true/false, 站点是否已经激活
      - `disabled` - `optional` true/false, 站点是否被禁用
      - `company` - `optional` 站点的company
      - `website` - `optional` 站点的website
      - `country` - `optional` 站点的country
      - `timezone` - `optional` 站点的默认时区
      - `contact.firstName` - `optional` 站点联系人的first name
      - `contact.lastName` - `optional` 站点联系人的last name
      - `contact.email` - `optional` 站点联系人的email
      - `contact.phone` - `optional` 站点联系人的phone number

  + Return

    返回修改后的Site对象


### Pay a site

  `PUT /api/v1/partner/sites/{site_id}/paying`

  + Parameters
    
    - `site_id` - `required` 站点的id
  
  + Return
  
    返回空

# UI Integration

  界面集成允许用户直接引用LiveChat后台页面, 可以放在iframe里, 也可以直接放一个链接在浏览器中打开, 需要Partner可以直接打通Agent的登录状态, 定制自己的样式, 控制隐藏不需要的头, 菜单, 脚。


## Agent SSO Login & Logout

  Partner可以配置自己管理的站点Agent使用统一的SSO来进行登录验证, 登录完成以后Comm100会维护自己的会话, 在会话存续期间不需要再次登录, 另外提供logout接口保证两边(Partner和Comm100)会话一致。Comm100提供了两种SSO登录验证集成的方式, 一种为基于[SAML2.0](https://en.wikipedia.org/wiki/SAML_2.0), 另一种为[JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

### SSO Flow

1. Agent打开Comm100页面, 如果判断当前用户没有登录会话就会就将浏览器重定向到SSO Login 页面 `https://partnerdomain.comm100.com/sso/login`, 在该页面会根据Partner的SSO配置情况再跳转到具体的Partner那边的登录页面
  - 如果配置的 SSO 为 SAML 则生成SAML Request提交到 SAML 的 loginUrl
  - 如果配置的 SSO 为 JWT 就会跳转到 JWT 的 loginUrl

2. Partner验证当前用户是否已经登录
  - 如果已经登录则直接生成认证的信息跳回到Comm100
  - 如果未登录则跳转到Partner的登录页面, 待Agent输入登录信息以后再生成认证信息调回到Comm100

3. Comm100接收SSO的认证信息, 验证有效性以后会为Agent生成会话, 如果验证不通过则显示具体的错误信息
  - SAML 接收认证信息的地址为 `https://partnerdomain.comm100.com/sso/saml/acs`
  - JWT 接收认证信息的地址为 `https://partnerdomain.comm100.com/sso/jwt`

4. Agent的授权不由SSO控制, 而是由该Agent在Comm100系统中的权限控制

5. 在会话存续期间, Agent可以一直访问Comm100中的受限资源

6. Agent从Comm100登出, 在清除会话以后会跳转到SSO中配置的Logout页面
  - 如果配置的 SSO 为 SAML 则会跳转到 SAML 的logoutUrl
  - 如果配置的 SSO 为 JWT 就会跳转到 JWT 的logoutUrl

7. 如果Agent从Partner IDP登出, Comm100提供以下接口供其销毁该会话, 用户需要在客户端浏览器访问这个接口才能有效清除会话
  `https://partnerdomain.comm100.com/sso/logout`

#### SAML 2.0

* SAML Response 参数说明
  - `NameID` - 必须为该Agent在Partner系统中的唯一Id, Comm100会根据这个Id来检索Agent, 如果系统中没有这个Agent时会创建对应的Agent
  - Supported user attributes 
    - `name`  - `required`, string, 登录agent的dispalyName
    - `email` - `required`, string, 登录agent的email
    - `firstName` - `optional`, string, 登录agent的firstName
    - `lastName`  - `optional`, string, 登录agent的lastName
    - `phone` - `optional`, string, 登录agent的phone
    - `title` - `optional`, string, 登录agent的title
    - `bio` - `optional`, string, 登录agent的bio
    - `avatar` - `optional`, string, 登录agent的头像地址
    - `isAdmin` - `optional`, boolean, 登录的agent是否为administrator

#### JWT

* JWT 接收Token接口参数说明
  `https://hosted.comm100.com/sso/jwt?jwt={jwt_token}&return_to={return_to_url}`

  - `jwt`
      jwt中包含三部分内容：Header、Payload和Signature。将header和payload使用Base64编码，Signature根据编码后的header、payload和特定的密钥(Comm100为每个Partner生成的特定密钥)，使用header中指定的签名算法(HS256)进行签名。
  - `return_to` - 身份验证通过后浏览器重定向的页面

* JWT Header
  
  ```json
  {
    "typ": "JWT",
    "alg": "HS256" // 加密算法, Comm100仅支持HS256
  }
  ```

* JWT Payload 参数说明
  - `iss` - jwt 签发者
  - `iat` - jwt 的签发时间
  - `jti` - `required` jwt 的唯一身份标识, 防止重放攻击
  - `nbf` - jwt 在该时间之前不能使用, 否则无效
  - `exp` - jwt 的过期时间, 这个过期时间必须要大于签发时间
  - `ssoId` - `required`, 登录Agent在用户系统中的唯一标识，在系统中不存在该Agent时会根据以下信息创建
  - `email` - `required`, 登录Agent的email
  - `name` - 登录Agent的display name
  - `phone` - 登录Agent的phone number
  - `firstName` - 登录Agent的first name
  - `lastName` - 登录Agent的last name
  - `title` - 登录Agent的title
  - `bio` - 登录Agent的bio
  - `avatar` - 登录agent的头像地址
  - `isAdmin` - 登录的用户是否为admin


* 手机和Desktop关于SSO登录的实现

  手机端和Desktop针对上述SAML 2.0 和 JWT 的实现方案采用与浏览器一致的方式, 在APP中打开页面进行SSO的认证, 认证结束以后再通过特定的接口登录到Chat Server中。Desktop可以直接打开SSO Login页面, 而手机则通过WebView加载SSO Login页面来通过验证。

  1. APP一般为每一个客户端单独编译, 编译时会指定partner的server地址, 如`https://partnerdomain.comm100.com/`
  
  2. 在APP打开以后会访问Server获取该Partner的SSO配置信息 `GET https://partnerdomain.comm100.com/sso/type` , 该接口返回如下数据

      ```json
      {
        "sso": "SAML" // "JWT", "None"
      }
      ```

  2. 如果配置了SSO, APP会通过SSO Login进行SSO登录验证, 登录地址 `https://partnerdomain.comm100.com/sso/login?appendChatServerToken=true&return_to={return_to_url}`
    
    - iOS和Android会在WebView中加载上述登录页面, `return_to_url`为 `comm100://login`
    - Desktop 直接打开上述登录页面, `return_to_url`为桌面版的主页面 `https://partnerdomain.comm100.com/routerserver/electron/index.html`

  3. 登录完成根据`appendChatServerToken`参数的值, 如果为true, 需要后端去chatserver请求一个token, 请求方法 `GET https://partnerdomain.comm100.com/chatserver/acquireToken?agentId={id}&magicString={xxx}`, 这是一个内部接口, 需要使用magicString来验证, 避免外部调用, 返回Token

      ```json
      {
        "token": "xxx"
      }
      ```
   
  4. 然后将token的值附加到上述的`retur_to`的url中, 即跳回到`comm100://login?token={token}`或者 `https://partnerdomain.comm100.com/routerserver/electron/index.html?token={token}`

  5. APP获取到这个`token`以后通过`token`直接登录, 该接口已经存在, 在手机端续会话时使用


* 移动端接收Token

  移动App增加直接接收JWT/Chat Server Token登录认证的接口：  

   `comm100://login?jwt=xx.xxx.xx` or `comm100://login?token=xxx`

  这个JWT接口允许用户在打开上面的login接口的时候跳转到自己的APP中, 实现从自己的APP验证的业务, 用户验证完以后通过上面地址跳转回Comm100 APP
    
 iOS  
    
  ```objective-c
     NSURL* url = [NSURL URLWithString: @"comm100://login?jwt=xxx.xxx.xx"];  
     [[UIApplication sharedApplication] openURL: url];  
  ```

  Android 
    
  ```java
    Intent intent = new Intent(Intent.ACTION_VIEW,Uri.parse("comm100://login?jwt=xxx.xxx.xx"));
    startActivity(intent); 
  ```
  

## Remove Header, Menu, Footer

后台页面区分有没有Header/Menu/Footer采用加载不同的MasterPage来实现, 目前的MasterPage已经提取到公共部分, 后台模块加载MasterPage文件时通过虚拟目录文件来实现, 因此针对不显示Header/Menu/Footer的后台页面只需要动态虚拟不同的MasterPage文件即可。

在使用页面集成时, 用户可以通过传入`bodyOnly`参数来控制显示的页面只有主体部分。`https://partnerdomain.comm100.com/livechat/campaign.aspx?siteId=1000001&bodyOnly=true&planId=1`, 虚拟目录提供者只要根据这个参数返回对应的文件。

### 多个MasterPage的技术实现

  1. 将后台的MasterPage抽象一个接口 IMasterPage, 接口包含当前BackMasterPage中的所有函数
  2. 修改BackMasterPage实现IMasterPage接口, Content Page中访问MasterPage中的函数时转化为IMasterPage而不是BackMasterPage
  3. 创建一个新的BodyOnlyMasterPage实现IMasterPage接口, 该页面不包含菜单, 不包含头部以及脚, 只加载必要的CSS, JS, 以及页面的成功/失败模块, Content等主体中的公共内容, 关于Content Page中用到的操作菜单之类的功能只需要实现为空就可以
  4. 修改MasterPageVirtualPathProvider文件的逻辑, 在接收到`bodyOnly`为true时, 返回BodyOnlyMasterPage, 否则返回BackMasterPage.
  5. 修改拼接URL的地方, 当`bodyOnly`为true时, 所有链接的地址也需要带上`bodyOnly=true`


## CSS Control

因为后台样式设计大量的CSS文件, 以及对应的图片/字体等资源, 所以针对Partner自定义的样式资源文件采用文件系统管理, 而不是通过数据库管理, 数据库会为每一个Partner存放一个Theme名字, 这个名字会对应到具体文件中的一个目录, 在加载某一个Partner的样式时会根据Partner的配置rewrite到具体的目录下面。

## Integrable Pages

目前的部分后台页面url中的命名不是很规范, 这些项目从一开始到现在已经有很多概念或者页面变更, 公开给partner用来集成来说不是很友好, 所以可以适当调整一下这些页面的名字和地址

| Page | Module | Url | Parameters |
| :- | - | - | :- |
| Dashboard | LiveChatDashboard | `/dashboard.aspx` | `siteId` |
| APPs | LiveChat | `/apps.aspx` | `siteId` |
| Installation | LiveChatFunc | `/installation.aspx` | `siteId` |
| Campaign - Chat Button | LiveChatFunc | `/campaign/chatButton.aspx` | `siteId` `campaignId` |
| Campaign - Chat Window | LiveChatFunc | `/campaign/chatWindow.aspx` | `siteId` `campaignId` |
| Campaign - Pre-Chat | LiveChatFunc | `/campaign/preChat.aspx` | `siteId` `campaignId` |
| Campaign - Post Chat | LiveChatFunc | `/campaign/postChat.aspx` | `siteId` `campaignId` |
| Campaign - Offline Message | LiveChatFunc | `/campaign/offlineMessage.aspx` | `siteId` `campaignId` |
| Campaign - Invitation | LiveChatFunc | `/campaign/invitation.aspx` | `siteId` `campaignId` |
| Campaign - Agent Wrap-up | LiveChatFunc | `/campaign/wrapup.aspx` | `siteId` `campaignId` |
| Campaign - Language | LiveChatFunc | `/campaign/language.aspx` | `siteId` `campaignId` |
| Campaign - Routing Rules | LiveChatFunc | `/campaign/routingRules.aspx` | `siteId` `campaignId` |
| Campaign - Chatbot | LiveChatFunc | x | |
| Campaign - KB Integration | LiveChatFunc | x | | 
| Campaign - Multiple Campaigns | LiveChatFunc | `/campaigns.aspx` | `siteId` |
| Canned Message | LiveChat | `/cannedMessages.aspx` | `siteId` |
| Departments | LiveChat | `/departments.aspx` | `siteId` |
| Auto Accept Chats | LiveChat | `/autoAcceptChats.aspx` | `siteId` |
| Custom Away Status | LiveChat | `/customAwayStatus.aspx` | `siteId` |
| AutoTranslation | LiveChat | x | |
| Audio & Video Chat | LiveChat | x | |
| Ban List | LiveChat | `/banlist.aspx` | `siteId` | 
| Conversions | LiveChat | `/conversions.aspx` | `siteId` |
| Visitor Segmentation | LiveChat | `/visitorSegmentations.aspx` | `siteId` |
| Visitor SSO | LiveChat | `/visitorSSO.aspx` | `siteId` |
| Chatbot | LiveChat | x | |
| Secure Forms | LiveChat | `/secureForms.aspx` | `siteId` |
| Credit Card Masking | LiveChat | x | |
| Permission | LiveChat | x | | 
| Chats | Report | `/chats.aspx` | `siteId` |
| Messages | Report | `/messages.aspx` | `siteId` |
| Missed & Refused | Report | `/missedAndRefusedChats.aspx` | `siteId` |
| Agent Chats | Report | `/agentChats.aspx` | `siteId` |
| Report - Real Time | Report | `/realTime.aspx` | `siteId` |
| Report - Chat Volume | Report | `/chatVolume.aspx` | `siteId` |
| Report - Chat Source | Report | `/chatSource.aspx` | `siteId` |
| Report - Queue | Report | `/queue.aspx` | `siteId` |
| Report - Wait Time | Report | `/waitTime.aspx` | `siteId` | 
| Report - Chat Transfer | Report | `/chatTransfer.aspx` | `siteId`|
| Report - Availability | Report | `/availability.aspx` | `siteId` |
| Report - Agent Performance | Report | `/agentPerformance.aspx` | `siteId` |
| Report - Workload | Report | `/workload.aspx` | `siteId` | 
| Report - Efficiency | Report | `/efficiency.aspx` | `siteId` |
| Report - Rating | Report | `/rating.aspx` | `siteId` | 
| Report - Post Chat Survey | Report | `/postChatSurvey.aspx` | `siteId` |
| Report - Pre-Chat Survey | Report | `/preChatSurvey.aspx` | `siteId` |
| Report - Wrap-Up | Report | `/wrapup.aspx` | `siteId` |
| Report - Conversion | Report | `/conversion.aspx` | `siteId` |
| Report - Manual Invitation | Report | `/manualInvitation.aspx` | `siteId` |
| Report - Auto Invitation | Report | `/autoInvitation.aspx` | `siteId` |
| Report - Offline Message | Report | `/offlineMessage.aspx` | `siteId` |
| Report - Canned Message | Report | `/cannedMessage.aspx` | `siteId` | 
| Report - Chatbot | Report | x | |


## Agent Console Integration API

### Iframe Load Agent Console

Partner可以使用iframe的方式加载Agent Console

`https://partnerdomain.comm100.com/livechat/agentconsole.aspx?siteId={siteId}`

Parameters:
  - `modules` - 显示的模块可以为`visitors`, `chats`, `agents`, `header`, `tab` 可以为多个, 以逗号隔开, 默认包含所有模块
    - `header` - 头部
    - `nav` - 左侧导航栏, 只有包含了该模块才会显示左侧的菜单
    - `visitors` - visitors 模块
    - `chats` - my chats 模块
    - `agents` - agents 模块


### Agent Console SDK

在页面上使用iframe加载Agent Console以后, Partner可以在宿主页面中引用Agent Console SDK的js文件来跟Iframe中的Agent Console进行交互

#### 初始化

只需要在SDK的js文件加载完成以后调用即可。

```javascript
  const app = Comm100AgentConsoleAPI.init();
```

#### General

* Properties

  - 获取或者设置Agent状态

  ```javascript
    cosnt status = 'away';
    app.set('agentconsole.status', status);
  ```

  - 获取或者设置当前选中的模块

  ```javascript
    const module = 'visitors,chats'; // chats
    app.set('agentconsole.modules', module);
  ```

  - 获取或设置当前选中的模块

  ```javascript
    app.set('agentconsole.nav', 'visitors');
  ```

* Actions

  - Agent 登录

  ```javascript
  app.do('agentconsole.login',{
        type: 'jwt',
        data: 'xxx.xxx.xx',
        // type: 'password',
        // data: {
        //   email: 'allon@comm100.com',
        //   password: 'Aa000000',
        // },
      }
    );
  ```

  - Agent 登出

  ```javascript
  const force = true;
  app.do('agentconsole.logout', force);
  ```

* Events

  - Agent 状态变化
  
  ```javascript
  app.on('agentconsole.agent.statusChanged', (newStatus) => {});
  ```


#### Visitors Module

* Properties

  - Filter - 可以获取或者设置当前访客列表的Filter条件
  ```javascript
  const filterCondition = app.get('agentconsole.visitors.filter');

  const filter = {
    type: 'custom', // 'all_visitors', 'all_chats', 'my_chats'
    conditions: [
      {
        field: 'Status',
        operate: 'is',
        value: 'Transferring',
      },
      // ...
    ],
  };

  app.set('agentconsole.visitors.filter', filter);
  ```

  - Columns - 可以获取或者设置当前访客列表显示的列信息

  ```javascript
  const visibleColumns = app.get('agentconsole.visitors.columns');

  const columns = [
    {
      field: 'Status',
      width: 40,
    },
    {
      field: 'Info',
      with: 300,
    },
    // ...
  ];

  app.set('agentconsole.visitors.columns', columns);
  ```
    
  - Visitors - 可以获取当前列表中的所有访客

  ```javascript
    const visitors = app.get('agentconsole.visitors');
  ```

* Events

  - 访客进入站点
  ```javascript
  app.on('agentconsole.visitor.enterSite', function(visitor) {} );
  ```

  - 访客细分变化
  ```javascript
  app.on('agentconsole.visitor.segmentChanged', function(visitor) {} );
  ```

  - 访客离开站点
  ```javascript
  app.on('agentconsole.visitor.outOfSite', function(visitor) {} );
  ```

#### Chats Module

* Properties

  - 获取当前列表中的所有正在进行的聊天
  ```javascript
  const chats = app.get('agentconsole.chats');
  ```

  - 获取或者设置当前选中的聊天
  ```javascript
  const selectedId = app.get('agentconsole.chats.selectedId')

  const chatId = 111;
  
  app.set('agentconsole.chats.selectedId', chatId);
  ```

* Events

  
  - 请求聊天
  ```javascript
  app.on('agentconsole.chat.request', function(visitor) {} );
  ```

  - 聊天进入队列
  ```javascript
  app.on('agentconsole.chat.enterQueue', function(visitor) {} );
  ```

  - 聊天有新的回复
  ```javascript
  app.on('agentconsole.chat.newResponse', function(visitor) {} );
  ```

  - 聊天被转移
  ```javascript
  app.on('agentconsole.chat.transferring', function(visitor) {} );
  ```

  - selectChanged - 当前选中的聊天切换
  ```javascript
  app.on('agentconsole.chats.selectChanged', function(chat) {} );
  ```
  - chatStarted - 聊天开始
  ```javascript
  app.on('agentconsole.chats.chatStarted', function(chat) {} );
  ```

  - chatEnded - 聊天结束
  ```javascript
  app.on('agentconsole.chats.chatEnded', function(chat) {} );
  ```

  - messageReceived - 收到聊天消息
  ```javascript
  app.on('agentconsole.chats.messageReceived', function(chatId, message) {} );
  ```

* Actions

  - 向指定聊天发送一条文本消息
  ```javascript
    app.do('agentconsole.chats.send', chatId, message);
  ```
  - 向指定的聊天输入框中填入消息
  ```javascript
    app.do('agentconsole.chat.input', chatId, message);
  ```
  - 在指定的聊天输入框中插入消息
  ```javascript
    app.do('agentConsole.chat.insert', chatId, message);
  ```


# API Integration

Comm100允许客户使用RESTful API管理用户数据, API的验证跟常规的RESTful API验证一样, 针对Partner这种第三方的场景只能使用Oauth来进行，但是Partner操作自己用户的Comm100站点时应该是Trust的，所以会跳过Agent Approve的过程, 即在Oauth 的流程中, 会进行验证和授权的过程, 但是对Agent的用会来说是没有知觉的。

## Credential
### Oauth Client

Partner 可以在自己的管理界面中增加一个Oauth Client, 获得对应的client_id和client_secret。

  - `redirect_uri` - 授权以后跳转的uri, 一般为用户的
  - `client_id` - 系统自动生成, Oauth中的client_id
  - `client_secret` - 系统自动生成, Oauth中的client_secret

### Oauth Flow

在调用Comm100 RESTful API之前, Agent首先需要获取到凭证`access_token`, 获取`access_token`的方式有多种, 根据不同的场景, 用户可以选择不同的流程来获取`access_token`

#### Implicit Grant

针对Web Application, 操作者一般为Agent, 一般会推荐用户使用Implicit Grant Flow, 以下为具体的流程

1. 浏览器请求Agent授权界面 `https://partnerdomain.comm100.com/oauth/authorize?grant_type={token}&siteId={site_id}&client_id={client_id}&redirect_uri={redirect_uri}`
2. Comm100根据SiteId验证当前浏览器的Agent是否已经登录, 如果未登录就跳转到登录页面
  - 如果Partner启用了SSO则跳转到Partner的SSO Login页面进行登录, 走SSO的流程, [SSO Login](#sso-settings)
  - 如果Partner未启用SSO则跳转到后台登录页面要求登录
3. 如果用户已经登录, 或者通过上述登录过程登录以后跳转回`authorize`地址, 验证`client_id` 和 `redirect_uri`是否正确, 如果正确就跳转到{redirect_uri}中配置的地址`{redirect_uri}?token={oauth_token}`
  - 这里如果是一般的client就会显示授权界面, 只有agent授权以后才会跳转到`redirect_uri`, Partner 生成的client对Partner下面的站点不需要agent手动授权, 而是自动通过
4. 用户在`redirect_uri`页面接收到`oauth_token`从而获得`access_token`, Partner可以在Server端或者Client端获取到, 从而调用Comm100 API
  - 这个`access_token`是由agent授权, 权限受限于当前agent的权限

#### Client Credentials

针对Server端应用, 操作者为Partner的管理员或者系统, 这个时候要操作用户数据就没办法获得用户授权, 所以可以使用Client Credentials Flow, 这种情况可能的场景的Partner的管理员或者系统需要访问客户的Comm100数据, 如获取代码贴到页面上, 同步Agent的信息, 或者其他的一些数据。

 访问`https://partnerdomain.comm100.com/oauth/token?grant_type={client_credentials}&client_id={client_id}&client_secret={client_secret}&siteId={siteId}`直接获取Token, 该token只能访问这个站点的数据, 无法访问其他站点的数据

## API Endpoint

### Configuration API

  - [LiveChat API](https://github.com/hgq719/docs/blob/master/LivechatAPIForPartner.md)
  - [Account API](https://github.com/hgq719/docs/blob/master/Account%20API.md)

### Report API
  用户可以通过Report API获取到报表数据，集成到自己的报表系统中，ReportApi的详情可参考[Report API](https://github.com/Comm100/restful-api/blob/master/report-api.md)。
