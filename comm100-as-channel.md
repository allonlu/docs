
角色：
1. Comm100：
   部署形式：
   - 独立部署数据库及应用

2. Partner：合作伙伴-需要集成Comm100产品的客户，如某个PhoneCall公司。
    访客
    客服
    Admin

开户：
   1. 创建Site，Partner可以更新Site信息

账号集成（Agent、Admin、Department）
  1. 新建账号：Partner可以直接通过接口创建Comm100的Agent，删除或修改Agent
     - New Agent ：Partner可以在界面中选择自己系统中的某个/某些账户直接生成对应的Comm100 Agent
     - Delete Agent：Partner在删除自己的账户的同时删除相应的Comm100 Agent；或者只删除Comm100 Agent
     - New Department: Partner可以在界面中新建一个Comm100 Department，也可以选择自己的系统中对应的Department生成对应的Comm100 Department,生成的同时可以选择性将该部门下面的人员自动生成Comm100 Agent并加入到当前部门中
     - Update Department：Partner可以在界面中更新Comm100 Department的信息，包括描述和Agents
     - Delete Department：Partner可以在界面中删除自己的部门的同时删除相应的Comm100 Department；或者只删除Comm100 Department
  2. 已有账号生成：Partner可以对自己系统中已有的账户创建Comm100的相应账户
  3. 权限：Partner可以在界面中配置自己系统中的账户在Comm100中权限
  4. 账号登录   
     - 普通账号登录:Partner在登录自己的系统后，可以直接访问自己的Comm100系统，不需要再进行登录

功能集成：
1. 集成商客户
  客户可以通过网页中的小组件与客服进行聊天
   +  livechat Code（installation）：Partner可以在界面中查看对应Campaign的InstallationCode并预览相应效果
2. 集成商客服
  将聊天功能集成到集成商的界面中，客服可以通过该界面直接和客户进行聊天、查看访客列表信息
   + List
     * visitors：Partner可以在自己的界面中嵌入访客列表的界面，其Agent可以查看访客列表，并对访客列表进行相应操作
     * mychats：Partner可以在自己的界面中嵌入聊天窗口，其Agent可以与访客进行聊天
   + tabs
     * info：Partner的Agent可以在聊天窗口页面查看当前聊天的信息
     * canned：Partner的Agent可以在聊天页面使用canned messages
     * wrap-up
   + 状态切换： Partner可以在界面中切换Comm100 Agent的状态或者切换自己系统中的状态的同时通过接口来改变Comm100 Agent的状态
   * Settings： Partner可以在界面中嵌入Comm100 Agent Console中的Settings页面
3. 集成商管理员
   各种配置界面、管理界面
   - Site管理
     + Update Site Profile ：Partner可以通过界面修改Site信息，需要Comm100提供Site修改接口
   - Agents管理
   - 权限配置
     + Agent Permissions：Partner可以在界面中配置自己系统中的账户在Comm100中权限
     + IP Restrictions：Partner可以在界面中配置需要限制的IP地址
   - Campaign配置
     + Chat Button：Partner的admin可以在界面中配置某个Campaign的Chat Button
     + Chat Window：Partner的admin可以在界面中配置某个Campaign的Chat Window
     + Pre-Chat：Partner的admin可以在界面中配置某个Campaign的Pre-Chat
     + Post-Chat：Partner的admin可以在界面中配置某个Campaign的Post-Chat
     + Offline Message：Partner的admin可以在界面中配置某个Campaign的Offline Message
     + Invitation：Partner的admin可以在界面中配置某个Campaign的Invitation
     + Agent Wrap-up：Partner的admin可以在界面中配置某个Campaign的Wrap-up
     + Language：Partner的admin可以在界面中配置某个Campaign的Language
     + Routing Rules：Partner的admin可以在界面中配置某个Campaign的Routing Rules
     + ChatBot：Partner的admin可以在界面中配置ChatBot的配置信息
     + Multiple Campaigns：Partner的admin可以在界面中配置是否需要使用多个Campaign
   - Settings配置
     + Canned Messages：Partner的admin可以在界面中配置Canned Messages
     + Departments
     + Auto Allocation
     + Custom Away Status
     + Auto Translation
     + Ban List
     + Visitor Sementation
     + Visitor Single Sign-On
   - Reports：用户可以通过ReportApi获取到报表数据，集成到自己的报表系统中
