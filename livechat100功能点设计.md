# 数据库

1. 在t_Billing_Plan表增加以下列

| Column | Type | Allow Nulls | Default | Description |
| - | - | - | - | - |
| ReportFeaturePoints | bigint | N | 0 | 该字段控制Report中的功能点，具体的值可以参考下面的列表 |
| HistoryStorageTime | int | N | 0 | 该字段控制历史数据的保存时间，单位为月，0表示不限制。用户不能查询超过这个时间范围的记录 |
| MaxAgent | int | N | 0 | 该字段用来限制plan的最大agent数量，0表示不限制 |
| InSale | bit | N | 1 | 该字段用来表示是否为正在销售的Plan, 这些Plan可以在后台直接选择 |

## Feature Point

| LiveChatFeaturePoint | VALUE | USING | FeaturePoint | Add/Change | Start | Standard | Pro | Team | Businuess | Enterprise | Upgrade |
| - | :-: | :-: | - | :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| IfCanUseAdvanceReport             | 1      | N | EnableAgentPermission        | C | N | Y | Y | Y | Y | Y | U1 |
| IfEnableDepartment                | 1 < 1  | Y | EnableDepartment             | - | N | N | Y | N | Y | Y | -  |
| IfCanCustomStyle                  | 1 < 2  | N | EnableAutoInvitation         | C | N | Y | Y | Y | Y | Y | U2 |
| IfCanCustomAutoInvitationRules    | 1 < 3  | Y | EnableAutoInvitationRules    | - | N | N | N | N | Y | Y | -  |
| IfCanCustomFields                 | 1 < 4  | Y | EnableCustomFields           | - | N | Y | Y | N | Y | Y | -  |
| IfEnableRating                    | 1 < 5  | Y | EnableRating                 | - | N | Y | Y | N | Y | Y | -  |
| IfEnableInvitation                | 1 < 6  | Y | EnableManualInvitation       | - | N | N | N | Y | Y | Y | -  |
| IfEnableMultipleCodePlan          | 1 < 7  | Y | EnableMultipleCampaigns      | - | N | N | Y | Y | Y | Y | -  |
| IfCanEmailtranscript              | 1 < 8  | Y | EnableEmailTranscript        | - | Y | Y | Y | Y | Y | Y | -  |
| IfCanQueryAllTranscript           | 1 < 9  | N | EnableCustomSMTPServer       | C | N | N | N | Y | Y | Y | U3 |
| IfCanUseReport                    | 1 < 10 | N | EnableSimpleRoute            | C | N | N | Y | Y | Y | Y | U4 |
| IfCanUseDesktopClient             | 1 < 11 | Y | EnableDesktopClient          | - | Y | Y | Y | Y | Y | Y | -  |
| IfCanUseMobileClient              | 1 < 12 | Y | EnableMobileClient           | - | Y | Y | Y | Y | Y | Y | -  |
| IfCanUseAutoAcceptChats           | 1 < 13 | Y | EnableAutoAccept             | - | N | N | N | Y | Y | Y | -  |
| IfCanUseAdvancedChatFunc          | 1 < 14 | Y | EnableAdvancedChatFunc       | - | N | Y | Y | Y | Y | Y | -  |
| IfCanBan                          | 1 < 15 | Y | EnableBan                    | - | Y | Y | Y | Y | Y | Y | -  |
| IfEnableTeamwork                  | 1 < 16 | Y | EnableTeamwork               | - | N | Y | Y | Y | Y | Y | -  |
| IfRemovePoweredby                 | 1 < 17 | Y | EnablePoweredByRemoval       | - | N | N | N | N | Y | Y | -  |
| IfRecordNavigation                | 1 < 18 | Y | EnableRecordNavigation       | - | N | N | N | N | N | N | -  |
| IfEnableExport                    | 1 < 19 | Y | EnableExportHistory          | - | Y | Y | Y | Y | Y | Y | -  |
| IfEnableIntegrations              | 1 < 20 | Y | EnableIntegrations           | - | Y | Y | Y | N | Y | Y | -  |
| IfEnableJavascriptAPI             | 1 < 21 | Y | EnableCustomVariable         | C | N | N | N | N | Y | Y | U5 |
| IfEnableMaxOn                     | 1 < 22 | Y | EnableMaxOn                  | - | Y | Y | Y | N | N | Y | -  |
| IfEnableCustomRoute               | 1 < 23 | Y | EnableRoutingRules           | - | N | N | N | N | N | Y | -  |
| IfEnableChatTranslation           | 1 < 24 | Y | EnableChatTranslation        | - | N | N | N | N | Y | Y | -  |
| IfEnableScreenSharing             | 1 < 25 | Y | EnableScreenSharing          | - | Y | Y | Y | N | Y | Y | -  |
| IfEnableEnableCustomCSS           | 1 < 26 | Y | EnableCustomCSS              | - | N | N | N | N | Y | Y | -  |
| IfEnableCannedMessageShortcuts    | 1 < 27 | Y | EnableCannedMessageShortcut  | - | N | N | Y | N | Y | Y | -  |
| IfEnableWrapupChat                | 1 < 28 | Y | EnableWrapup                 | - | N | N | Y | N | Y | Y | -  |
| IfEnableAwayStatus                | 1 < 29 | Y | EnableCustomAwayStatus       | - | N | Y | Y | Y | Y | Y | -  |
| IfEnableDashboardCustomMetric     | 1 < 30 | Y | EnableCustomMatrics          | - | N | N | N | N | N | Y | -  |
| IfEnableRecordOperatorMessage     | 1 < 31 | Y | EnableRecordAgentMessage     | - | N | Y | Y | N | Y | Y | -  |
| IfEnableTransferToDepartment      | 1 < 32 | N | EnableDomainRestriction      | C | N | N | Y | Y | Y | Y | U6 |
| IfEnablePCIForm                   | 1 < 33 | Y | EnablePCIForm                | - | N | N | Y | N | N | Y | -  |
| IfEnableSSO                       | 1 < 34 | Y | EnableSSO                    | - | N | N | N | N | N | Y | -  |
| IfEnableVisitorCustomFilter       | 1 < 35 | Y | EnableVisitorCustomFilter    | - | N | N | Y | N | Y | Y | -  |
| IfEnableVisitorSegment            | 1 < 36 | Y | EnableVisitorSegmentation    | - | N | N | N | N | N | Y | -  |
| IfEnableDynamicCodePlan           | 1 < 37 | Y | EnableDynamicCampaign        | - | N | N | N | N | N | Y | -  |
| IfEnableExtensions                | 1 < 38 | Y | EnableAgentConsoleExtension  | - | N | N | N | N | N | Y | -  |
| -                                 | 1 < 39 | N | EnableAutoAllocation         | A | N | N | N | N | N | Y | U7 |
| -                                 | 1 < 40 | N | EnableLiveChatAPI            | A | N | N | N | N | Y | Y | U8 |
| -                                 | 1 < 41 | N | EnableJavascriptAPI          | A | N | N | N | N | Y | Y | U9 |
| -                                 | 1 < 42 | N | EnablePasswordPolicy         | A | N | N | Y | N | Y | Y | UA |
| -                                 | 1 < 43 | N | EnableIPRestrictions         | A | N | N | Y | N | Y | Y | UB |
| -                                 | 1 < 44 | N | EnableAuditLog               | A | N | N | Y | Y | Y | Y | UC |


+ UX为针对Comm100平台的Plan进行对应feature ponint 的升级
  - U1. 为所有站点开启EnableAgentPermission功能
  - U2. 为所有站点开启EnableAutoInvitation功能
  - U3. 为所有站点开启EnableCustomSMTPServer功能
  - U4. 为所有站点开启EnableSimpleRoute功能
  - U5. 为Business和Enterprise站点开启EnableCustomVariable功能
  - U6. 为所有站点开启EnableDomainRestriction功能
  - U7. 为Enterprise站点和EnableRoutingRules开启的站点开启EnableAutoAllocation功能
  - U8. 为Business和Enterprise站点以及EnableCustomVariable开启的站点开启EnableLiveChatAPI功能
  - U9. 为Business和Enterprise站点开启EnableJavascriptAPI功能
  - UA. 为Business和Enterprise站点开启EnablePasswordPolicy功能
  - UB. 为Business和Enterprise站点开启EnableIPRestrictions功能
  - UC. 为所有站点开启EnableAuditLog功能

+ 具体Plan的FeaturePoint值（十进制）
  - Start: 39360768
  - Standard: 2723797301
  - Pro: 30836696669623
  - Team: 17597018537925
  - Business: 34126723547135
  - Enterprise: 35184371826687

## Report Feature Point
| ReportFeaturePoint | VALUE | Start | Standard | Pro | Team | Businuess | Enterprise |
| - | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| EnableExportReport                | 1      | N | Y | Y | Y | Y | Y |
| EnableDistributionDisplayReport   | 1 < 1  | N | N | N | Y | Y | Y |
| EnableReportRealTime              | 1 < 2  | N | Y | Y | Y | Y | Y |
| EnableReportChatVolume            | 1 < 3  | N | Y | Y | Y | Y | Y |
| EnableReportOfflineMessage        | 1 < 4  | N | Y | Y | Y | Y | Y |
| EnableReportAvailability          | 1 < 5  | N | Y | Y | Y | Y | Y |
| EnableReportAgentPerformance      | 1 < 6  | N | Y | Y | Y | Y | Y |
| EnableReportRating                | 1 < 7  | N | Y | Y | N | Y | Y |
| EnableReportPostChatSurvey        | 1 < 8  | N | N | Y | N | Y | Y |
| EnableReportPreChatSurvey         | 1 < 9  | N | N | Y | N | Y | Y |
| EnableReportWrapup                | 1 < 10 | N | N | Y | N | Y | Y |
| EnableReportCannedMessage         | 1 < 11 | N | N | Y | N | Y | Y |
| EnableReportRealTimeAgent         | 1 < 12 | N | N | N | N | Y | Y |
| EnableReportEfficiency            | 1 < 13 | N | N | N | N | N | Y |
| EnableReportWorkload              | 1 < 14 | N | N | N | N | N | Y |
| EnableReportQueue                 | 1 < 15 | N | N | N | N | N | Y |
| EnableReportChatSource            | 1 < 16 | N | N | N | N | N | Y |
| EnableReportWaitTime              | 1 < 17 | N | N | N | N | N | Y |
| EnableReportChatTransfer          | 1 < 18 | N | N | N | N | N | Y |
| EnableReportManualInvitation      | 1 < 19 | N | N | N | N | N | Y |
| EnableReportAutoInvitation        | 1 < 20 | N | N | N | N | N | Y |


+ 具体Plan的ReportFeaturePoint
  - Start: 0
  - Standard: 253
  - Pro: 4093
  - Team: 127
  - Business: 8191
  - Enterprise: 2097151
