# 数据库

## t_MarktingPlace_Client 表

| Colume | Type | Allow Nulls | Default | Description |
| - | - | -| - | - |
| ClientId |
| SiteId |
| IfPrivate |
| Name |
| Description |

## t_Oauth_Client 表

| Colume | Type | Allow Nulls | Default | Description |
| - | - | -| - | - |
| ClientId | nvarchar(128) | N | '' | Primary Key, 标识每一个客户端App的Id |
| ClientSecret | nvarchar(64) | N | '' | 客户端App的访问密钥 |
| Trusted | bit | N | 0 | 标识客户端是否受信任，如果是受信任的客户端则会跳过用户Approve的页面，直接授权 |
| GrantTypes | nvarchar(1024) | N | '' | 客户端支持的Grant Type, 可选值包括authorization_code, password, implicit, client_credentials, 若支持多个用逗号(,)分割 |
| RedirectUri | nvarchar(2048) | N | '' | 客户端的重定向URI |
| Scopes | nvarchar(2048) | N | '' | 该APP允许使用的权限范围 |
| AccessTokenValidity | int | N | 60×60×2 | Access Token的有效时间，单位为s，默认为2小时 | 
| RefreshTokenValidity | int | N | 60×60×24×30 | Refresh Token的有效时间，单位为s，默认30天 |
| SupportedDomains | nvarchar(max) | N | '' | 客户端需要跨域的情况下, 用户可以配置的允许跨域的域名, 该域名会在AccessToken颁发时生成, 一旦生成不可改变 |

## t_Oauth_RefreshToken表    

| Colume | Type | Allow Nulls | Default | Description |
| - | - | -| - | - |
| Id | guid | N | '' | Primary key |
| ClientId | nvarchar(128) | N | '' | App Id |
| SiteId | int | N | 0 | Site Id |
| AgentId | int | N | 0 | Agent Id |
| Scope | nvarchar(2048) | N | '' | 权限范围，如果多个则用,分隔 |
| Expires | date | N | getdate() | 过期的时间 |

- 给Token字段建主键索引

# Endpoint

## /oauth2/auth

该页面让用户授权具体的权限给第三方APP, 需要展示APP申请的权限为哪些, 如果识别到用户还未登录则需要跳转到登录页面, 登录完成以后再跳转到这个页面

`GET /oauth2/auth?response_type=code&client_id=[client_id]&redirect_uri=[redirect_uri]&scope=[scopes]&state=[state]`

## /oauth2/token

该页面让用户获取Token, 支持的grant_type为 authorization_code, refresh_token, password, client_credentials,

`POST /oauth2/token?grant_type=refresh_token&refresh_token=[token]`


# Grant

## Authorization Code

1. Client要访问用户受保护的数据时，Client首先请求auth接口请求用户授权

    `GET /oauth2/auth?response_type=code&client_id=[client_id]&redirect_uri=[redirect_uri]&scope=[scopes]&state=[state]`

    + 参数说明
        - response_type: `REQUIRED`, 值必须为`code`
        - client_id: `REQUIRED`, 用户申请client时填写的client id
        - redirect_uri: `REQUIRED`, 用户申请client时配置的redirect_uri, server会验证这个uri, 并且在授权以后跳转到这个地址
        - socpe: `OPTIONAL`, 请求的权限, 这些信息会显示在授权页面上
        - state: `COMMENDED`

2. 用户授权以后跳转到redirect_uri中, 并且在uri中带上code

    + Response说明, response信息会在重定向到redirect_uri时带上
        - code: `REQUIRED`, 授权服务器生成的code, client使用这个code换取refresh_token和access_token
        - state: `REQUIRED` 如果用户有传的话就必须要带上

3. Client使用code向token接口换取access_token和refresh_token

    `POST /oauth2/token?grant_type=authorization_code&code=[code]&redirect_uri=[redirect_uri]&client_id=[client_id]`
    
    + 参数说明
        - grant_type: `REQUIRED`, 值必须为`authorization_code`
        - code: `REQUIRED`, 上面获取的到code
        - client_id: `REQUIRED`, 用户申请client时填写的client id
        - redirect_uri: `REQUIRED`, 用户申请client时配置的redirect_uri, Authorization Server会验证这个uri

    + Response说明
        - access_token
        - token_type
        - expires_in
        - refresh_token


## Implicit

1. Client要访问用户受保护的数据时，Client首先请求auth接口请求用户授权

    `GET /oauth2/auth?response_type=token&client_id=[client_id]&redirect_uri=[redirect_uri]&scope=[scopes]&state=[state]`

    + 参数说明
        - response_type: `REQUIRED`, 值必须为`token`
        - client_id: `REQUIRED`, 用户申请client时填写的client id
        - redirect_uri: `OPTIONAL`, 如果有就跳转到用户该页面，如果没有则跳转到client中配置的redirect_uri
        - socpe: `OPTIONAL`, 请求的权限, 这些信息会显示在授权页面上
        - state: `COMMENDED`

2. 用户授权以后就直接跳转到redirect_uri

    + Response说明, 这些信息携带在uri中
        - access_token
        - token_type
        - expires_in
        - scope
        - state

## Client Credential

使用上述的ClinetId和ClientSecret来进行验证, 只有受信任的第三方应用可以使用, 一般为我们的内部应用或者为客户的私有应用

`POST /oauth2/token?grant_type=client_credentials&scope=[scope,scope]`

`headers: Basic BASE64([client_id] + ':' + [client_secret]) `

## Password Credential

使用用户的email和password来进行验证

`POST /oauth2/token?grant_type=password&client_id=[client_id]&client_secret=[client_secret]`

`headers: Basic BASE64([email] + ':' + [password]) `

## Chat Server Token

该授权类型为Comm100自定义, 使用chat_server的token来验证和授权, 仅供内部使用, Agent Console的前端集成Ticket/Social采用这种方式来验证和授权

`POST /oauth2/token?grant_type=chat_server_token&clinet_id=[client_id]`

`headers: Basic BASE64([server] + ':' + [token])`

# Access Token

Access Token中包含了一次授权的用户信息和授权的信息，应该包含以下内容然后进行加密，最后用户使用的access_token就是这个加密的结果，可以使用Owin框架下面的ClaimsIdentity来封装这些内容, `Authorization Server`的加密以及`Resource Server`上面的解密都由框架完成
- Site Id
- Agent Id
- Scopes
- Expires

# Scopes

权限的设定目前以产品来分隔即Social, Ticket, KownledgeBase, LiveChat, Account 目前的框架不限定Read/Write的权限分隔, 所以用户在使用的时候scope就这样传
scope=Ticket,Account

Ticket接口在处理具体的Method时需要校验上面的权限，不允许授权了LiveChat权限的access_token访问Ticket接口

# Resource Server

`Resource Server`使用Web API, 在Startup的时执行如下代码
```C#
public void ConfigureAuth(IAppBuilder app)
{ 
    app.UseOAuthBearerAuthentication(new Microsoft.Owin.Security.OAuth.OAuthBearerAuthenticationOptions());
}
```
上面的OAuthBearerAuthenticationOptions中可以增加自己的业务代码

Resource Server在接收到上面的access_token以后解密获取到上面的信息, 要求`Resource Server`与`Authorization Server`使用相同的密钥

# Client

针对Agent Console 中使用ticket api就需要使用上述的 chat_server_token的grant type



