# 总体设计

+ 所有需要控制的功能都通过功能点来控制，功能点采用行来设计，而不是原来的一个64位通过位运算来控制，这样便于以后的扩展以及测试检查数据
  - 原来通过t_Site_FunctionConfig来控制的功能修改为通过功能点来控制
  - 原来通过Plan中的Application Type (Team/Business/Enterprise)来控制的功能改为通过功能点来控制
  - 原来通过Add On来控制的功能通过功能点来控制，用户购买/取消Add on时直接修改功能点


# 数据库

1. 增加表t_Billing_FeaturePoint, 用来维护不同Plan/Addon的初始功能点状态

| Colume | Type | Allow Nulls | Default | Description |
| - | :- | - | - | :- |
| OwnerType | nvarchar(64) | N | '' | 表示该记录对应的类型，目前为Addon或者Plan |
| OwnerId | int | N | 0 | 对应PlanId或者AddonId |
| Product | int | N | 0 | 该字段表示功能点所属的产品，根据Product的枚举来赋值, 0 - admin, 1 - LiveChat |
| Feature | nvarchar(64) | N | '' | 表示具体的功能点，为系统定义的字符串常量 |
| IfEnable | bit | N | 0 | 该字段表示对应的Feature是否打开 |

2. 增加表t_Site_FeaturePoint，用来维护Feature站点的功能点，每一个站点的每个功能点都采用一条记录来维护

| Column | Type | Allow Nulls | Default | Description |
| - | :- | - | - | :- |
| SiteId | int | N | 0 | 该字段表示这条记录的SiteId，对SiteId做索引，优化查询 |
| Feature | nvarchar(64) | N | '' | 该字段为具体的功能点枚举，为系统定义的字符串常量 |
| IfEnable  | bit | N | 0 | 该字段表示对应的FeaturePoint是否打开 |

3. 在t_Billing_Plan表中增加以下字段

| Column | Type | Allow Nulls | Default | Description |
| - | :- | - | - | :- |
| MaxAgent | int | N | 0 | 该字段控制当前Plan最大允许的Agent数量, 0表示不限制 |
| InSale | bit | N | 0 | 该字段表示这个Plan是否为在售状态，只有在售状态的Plan才会在选择Plan的页面中展示出来 |

4. 在t_Billing_SitePlan表中增加一下字段

| Column | Type | Allow Nulls | Default | Description |
| - | :- | - | - | :- |
| MaxAgent | int | N | 0 | 该字段控制站点允许的Agent最大数量，0表示不限制 |

5. 在t_Billing_Plan表中删除以下无用的列

  - LiveChatFeaturePoint

6. 在t_Site_FunctionConfig表中删除以下列

  - LiveChat_IfEnableDepartment
  - LiveChat_IfCanCustomStyle
  - LiveChat_IfCanCustomAutoInvitationRules
  - LiveChat_IfCanCustomFields
  - LiveChat_IfEnableRating
  - LiveChat_IfCanUseAdvancedReport
  - LiveChat_IfRecordNavigation
  - LiveChat_IfRemovalPoweredby
  - LiveChat_IfCanUseDesktopClient
  - LiveChat_IfCanUseMobileClient
  - LiveChat_IfCanAutoAcceptChats
  - LiveChat_IfCanUseAdvancedChatFunc
  - LiveChat_IfCanEmailTranscript
  - LiveChat_IfCanBan
  - LiveChat_IfEnableMultipleCodePlan
  - LiveChat_IfEnableInvitation
  - LiveChat_IfEnableTeamWork
  - LiveChat_IfCanUseReport
  - LiveChat_IfCanQueryAllTranscript
  - LiveChat_IfEnableExport
  - LiveChat_IfEnableIntegrations
  - LiveChat_IfEnableJavascriptAPI
  - LiveChat_IfEnableCustomizedRouteRules
  - LiveChat_IfEnableMaximumOn
  - LiveChat_IfEnableChatTranslation
  - LiveChat_IfEnableScreenSharing
  - LiveChat_IfCanCustomCSS
  - LiveChat_IfEnableWrapupChat
  - LiveChat_IfEnableCannedMessageShortcuts




## Feature Point  

| FeaturePoint | Product | Start | Standard | Pro | Team | Businuess | Enterprise |
| - | :-: | :-: | :-: | :-: | :-: | :-: |
| AgentPermission             | 0 | N | Y | Y | Y | Y | Y |
| PasswordPolicy              | 0 | N | N | Y | N | Y | Y |
| IPRestrictions              | 0 | N | N | Y | N | Y | Y |
| AuditLog                    | 0 | N | N | Y | Y | Y | Y |
| Department                  | 1 | N | N | Y | N | Y | Y |
| AutoInvitation              | 1 | N | Y | Y | Y | Y | Y |
| AutoInvitationRules         | 1 | N | N | N | N | Y | Y |
| CustomFields                | 1 | N | Y | Y | N | Y | Y |
| Rating                      | 1 | N | Y | Y | N | Y | Y |
| ManualInvitation            | 1 | N | N | N | Y | Y | Y |
| MultipleCampaigns           | 1 | N | N | Y | Y | Y | Y |
| EmailTranscript             | 1 | Y | Y | Y | Y | Y | Y |
| CustomSMTPServer            | 1 | N | N | N | Y | Y | Y |
| SimpleRoute                 | 1 | N | N | Y | Y | Y | Y |
| DesktopClient               | 1 | Y | Y | Y | Y | Y | Y |
| MobileClient                | 1 | Y | Y | Y | Y | Y | Y |
| AutoAccept                  | 1 | N | N | N | Y | Y | Y |
| AdvancedChatFunc            | 1 | N | Y | Y | Y | Y | Y |
| Ban                         | 1 | Y | Y | Y | Y | Y | Y |
| Teamwork                    | 1 | N | Y | Y | Y | Y | Y |
| PoweredByRemoval            | 1 | N | N | N | N | Y | Y |
| RecordNavigation            | 1 | N | N | N | N | N | N |
| ExportHistory               | 1 | Y | Y | Y | Y | Y | Y |
| Integrations                | 1 | Y | Y | Y | N | Y | Y |
| CustomVariable              | 1 | N | N | N | N | Y | Y |
| MaximumOn                   | 1 | Y | Y | Y | N | N | Y |
| RoutingRules                | 1 | N | N | N | N | N | Y |
| ChatTranslation             | 1 | N | N | N | N | Y | Y |
| ScreenSharing               | 1 | Y | Y | Y | N | Y | Y |
| CustomCSS                   | 1 | N | N | N | N | Y | Y |
| CannedMessageShortcut       | 1 | N | N | Y | N | Y | Y |
| Wrapup                      | 1 | N | N | Y | N | Y | Y |
| CustomAwayStatus            | 1 | N | Y | Y | Y | Y | Y |
| CustomMatrics               | 1 | N | N | N | N | N | Y |
| RecordAgentMessage          | 1 | N | Y | Y | N | Y | Y |
| DomainRestriction           | 1 | N | N | Y | Y | Y | Y |
| PCIForm                     | 1 | N | N | Y | N | N | Y |
| SSO                         | 1 | N | N | N | N | N | Y |
| VisitorCustomFilter         | 1 | N | N | Y | N | Y | Y |
| VisitorSegmentation         | 1 | N | N | N | N | N | Y |
| DynamicCampaign             | 1 | N | N | N | N | N | Y |
| AgentConsoleExtension       | 1 | N | N | N | N | N | Y |
| AutoAllocation              | 1 | N | N | N | N | N | Y |
| LiveChatAPI                 | 1 | N | N | N | N | Y | Y |
| JavascriptAPI               | 1 | N | N | N | N | Y | Y |
| SocialMedia                 | 1 | N | N | N | N | N | Y |
| AudioVideoChat              | 1 | N | N | N | N | N | N |
| ChatBot                     | 1 | N | N | N | N | N | N |
| ExportReport                | 1 | N | Y | Y | Y | Y | Y |
| DistributionDisplayReport   | 1 | N | N | N | Y | Y | Y |
| ReportRealTime              | 1 | N | Y | Y | Y | Y | Y |
| ReportChatVolume            | 1 | N | Y | Y | Y | Y | Y |
| ReportOfflineMessage        | 1 | N | Y | Y | Y | Y | Y |
| ReportAvailability          | 1 | N | Y | Y | Y | Y | Y |
| ReportAgentPerformance      | 1 | N | Y | Y | Y | Y | Y |
| ReportRating                | 1 | N | Y | Y | N | Y | Y |
| ReportPostChatSurvey        | 1 | N | N | Y | N | Y | Y |
| ReportPreChatSurvey         | 1 | N | N | Y | N | Y | Y |
| ReportWrapup                | 1 | N | N | Y | N | Y | Y |
| ReportCannedMessage         | 1 | N | N | Y | N | Y | Y |
| ReportRealTimeAgent         | 1 | N | N | N | N | Y | Y |
| ReportEfficiency            | 1 | N | N | N | N | N | Y |
| ReportWorkload              | 1 | N | N | N | N | N | Y |
| ReportQueue                 | 1 | N | N | N | N | N | Y |
| ReportChatSource            | 1 | N | N | N | N | N | Y |
| ReportWaitTime              | 1 | N | N | N | N | N | Y |
| ReportChatTransfer          | 1 | N | N | N | N | N | Y |
| ReportManualInvitation      | 1 | N | N | N | N | N | Y |
| ReportAutoInvitation        | 1 | N | N | N | N | N | Y |

# 类设计

  - 用户注册某一个Plan时，就会将该Plan下面的所有FeaturePoint拷贝一份到t_Site_FeaturePoint
  - 用户切换Plan时，会将该Plan下面的所有FeaturePoint更新到t_Site_FeaturePoint
  - 用户购买Addon时，会将该Addon下面的所有FeaturePoint添加到t_Site_FeaturePoint中
  - 用户删除Addon时，会将该Addon下面的所有FeaturePoint从t_Site_FeaturePoint中删除
  - 新增功能点时需要同时为AddOn/Plan以及Site增加对应的FeaturePoint

1. 增加PlanFeature类

  ```c#
  public class PlanFeaturePoints {
    private int _planId;
    private Dictionary _features;
    public PlanFeaturePoints(int planId) {}
    public updateFeaturePoints(Dictionary features) {}
  }
  ```

2. 增加AddonFeature类

  ```c#
  public class AddonFeaturePoints {
    private int _addonId;
    private Dictionary _features;
    public AddonFeature(int planId) {}
    public updateFeatures(Dictionary features) {}
  }
  ```

3. 增加SiteFeature类

  ```c#
  public class SiteFeature {
    private int _siteId;
    private Dictionary _features;
    public SiteFeature(int siteId) {}
    public addAddon(int addonId) {}
    public remoteAddon(int addonId) {}
    public switchPlan(int planId) {}
  }
  ```

