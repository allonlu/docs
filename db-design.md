# 数据库设计文档评审

1. 项目中涉及到的所有数据库改动都需要通过评审 
2. 数据库设计文档需要包含某一产品下面的所有数据库表, 包括以前设计的表
3. 数据库设计文档可以以word/excel/md文档来组织, 只要能够说明需要的内容即可
4. 数据库设计文档需要包含以下内容
  - 表名, 字段名 - 表名和字段名应严格按照Comm100的命名规范来命名, 要能从名字中知道这个表/字段的用途
  - 字段类型 - nvarchar须在够用的前提下指定长度
  - 是否为空 - 原则上所有字段都不允许为空
  - 可能的值 - 如果是一些枚举值, 需要列出所有的值, 新加字段都建议使用string来管理, 便于查看, 而不需要再去代码里对哪个值对应什么内容
  - 默认值约束 - 如果为非空字段, 建议加上默认值约束
  - 主键约束 - 所有的表都建议加上主键约束, 便于后面转到EF, 考虑分布式或者数据库离线可以考虑使用guid代替int作为主键
  - 索引 - 针对可能的查询, 在表设计中需要清楚定义出索引
  - 说明 - 需要说明字段的用途, 以及字段的关系(如表示其他表的id)


以下为新增一个表的示例:

  + 新增表 `t_Oauth_Client`
    - 索引
      - PK_ClinetId(ClientId) - `ClientId` 主键索引
    - 表结构


  | Seq | Name | Type | Nullable | Default | Remark |
  | - | - | - | :-: | :-: | - |
  | 1 | `ClientId` | nvarchar(32) | no | newid() | Oauth Client Id, 该Id为系统生成的guid |
  | 2 | `Name` | nvarchar(256) | no | '' | Oauth Client 的名称 |
  | 3 | `Secret` | nvarchar(32) | no | newid() | Oauth Client Secret, 为系统生成的guid |
  | 4 | `Scopes` | nvarchar(256) | no | '' | Oauth Client 允许访问的数据范围 |
  | 5 | `GrantTypes` | nvarchar(256) | no | '' | Oauth Client 允许的授权方式, 可以为`authorization_code`, `implicit`, `password`, `client_credentials`及其组合, 以JSON数组形式组织 |
  | 6 | `RedirectUris` | nvarchar(max) | no | '' | Oauth Client 的token重定向地址, 允许配置多个, 以JSON数组的形式组织 |
  | 7 | `AccessTokenValidaty` | int | no | 60 * 60 * 12 | Oauth Client生成的Access Token的过期时间(s) |
  | 8 | `RefreshTokenValidaty` | int | no | 60 * 60 * 24 * 30 | Oauth Client生成的Refresh Token的过期时间(s) |
  | 9 | `IfTrusted` | bit | no | 0 | Oauth Client是否受信任, 如果是受信任的Client则会跳过User Grant |
  | 10 | `IfActive` | bit | no | 1 | Oauth Client是否可用 |
  | 11 | `CreateTime` | datetime | no | getutcdate() | Oauth Client的创建时间 |

  


