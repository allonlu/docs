# LiveChat

1. 增加 branding.yaml 文件，包含如下内容：
  + key: name 用于生成文件时的前缀，唯一
  + variables:
    - api: API 对象名称，字符串
    - entry: 入口使用的全局变量，comm100main
  + ids:
    - mobile-invitation: Comm100-mobile-invitation，字符串
    - mobile-invitation-content: Comm100-mobile-invitation-content，字符串
    - sandbox-iframe：bundle 文件运行所在的 iframe 的 id，字符串
    - ui-container: comm100-container，字符串
    - cookie-prefix：cookie prefix，字符串（optional 服务器可能不改）
    - session-storage-key: comm100SessionStorage，字符串
    - local-storage-key: comm100LocalStorage，字符串
  + links:
    - host-name: postMessage 监听的 host 地址（SSOLogin.jsx 和 Iframe.jsx 使用），字符串
    - powered-by-url: Powered-by 指向的地址，字符串
  + languages:
    - powered-by: Powered-by 文字内容，字符串
    - google-analytics-title: Google Analytics 使用的 Title，字符串
    - notification-tag: comm100_notify，字符串
    - popout-window-title: Comm100ChatWindow${siteId}，字符串
  + features:
    - dynamic-campaign: 是否可能使用 Dynamic Campaign，布尔值
    - group-tag: 是否可能使用 Group Tag，布尔值
    - custom-css: 是否可能使用 Custom CSS 功能，布尔值
    - javascript-api: 是否可能使用 JavaScript API 功能，布尔值
    - custom-variables: 是否可能使用 Custom Variables 功能，布尔值
    - remove-ads: 是否需要去除老版本广告的功能，布尔值（功能见 Button.jsx）

2. 根据 branding.yaml 的值和给定的 key 编译 js，用 --branding xxx 指定 key，默认使用第一个

  除生成原始文件之外，额外生成带 key 作为前缀的文件，标注 branding 信息。如 bundle.xxx.js 生成为 comm100.bundle.xxx.js

3. 根据 branding.yaml 的值和给定的 key 编译 html

  除生成原始文件之外，额外生成带 key 作为前缀的文件，标注 branding 信息

4. 根据 branding.yaml 修改代码

   1. 功能不能使用时，直接删除相关代码
   2. 文字部分通过 branding.yaml 的值配置，通过 `process.env.BRANDING.languages.xxx` 获取
   3. 其他数据也通过 `process.env.BRANDING` 提供，在编译时直接替换变量，不保存原始引用（使用 `rollup-replace-plugin` ）

5. 增加一个 release.js 文件，根据 branding.yaml 批量生成所有的 js 和 html

6. 根据服务器返回的 state.configuration.features.sendFile 控制文件上传，包括：

  + Footer 中的发文件 Icon
  + Textarea 的 Drag & Drop 功能

7. 根据服务器返回的 state.configuration.features.teamwork 控制是否开启访客 heartbeat 功能（不包括聊天窗口 heartbeat）

# Agent Console

1. 增加 branding/config.yaml 配置

   ```yaml
   -
     # unique name for each branding, will be used as:
     # + key for branding config in object
     # + suffix to files that are different under brandings
     #     example: index.html => index.comm100.html
     #     script will separate these files later using suffix (check split.files.js for details)
     name: &comm100-name comm100
     # logo used on top of header (left top corner)
     # will be used in all pages including oops.html, index.html, agentConsole.aspx, etc.
     # file will be compiled as base64 string in branding.js and used later in CSS or HTML
     logo: comm100.svg
     # notification icon used in web app (check window.icon for usage details)
     # Notice: electron app use different icon in app/icons/*.icon.png
     #         (check ChannelCenter.js `showNotification` for details)
     icon: comm100.png
     # manifest static files used for web application (service worker)
     # (check Chrome > DevTools > Application > Manifest for details)
     # Notice: these static files MUST contain suffix the same as `name` property defined above
     #         otherwise `split.files.js` cannot move these to different folders based on brandings
     assets: [logo256x256.comm100.png, manifest.comm100.json]
     # used as:
     #   - title of modal
     #   - title of notification
     #   - agent console name: e.g. `${branding} Agent Console`
     #   - product name
     #       example: `You want to leave ${branding}?`
     branding: &comm100-branding Comm100 Live Chat
     # theme color
     color: &comm100-color '#329fd9'
     # color lighter than theme color
     light-color: '#cce7f5'
     # color darker than theme color
     dark-color: '#0d92d8'
     # color for border of visitor text
     border-color: '#c0ddec'
     # color for online status icon
     # by default, this color is the same as theme color
     # however, it might have such case that theme color is similar to away status color
     # in those cases, change this color to some different color instead
     online-color: *comm100-color
     kb:
       # Homepage for Knowledge Base
       homepage: https://www.comm100.com/livechat/knowledgebase/
       # Link shown when login returns error
       login-error: https://www.comm100.com/livechat/knowledgebase/troubleshooting-how-to-resolve-error-2032.html
       # Link shown when choosing multiple accounts (for "May I keep one and close others?")
       cancel-account: https://www.comm100.com/livechat/knowledgebase/how-to-cancel-my-comm100-account.html
     links:
       # Link used when clicking on Logo on top left corner on page
       livechat: https://www.comm100.com/livechat
       main-route: https://route.comm100.com/routeserver/urlhandler.ashx
       backup-route: https://route1.comm100.com/routeserver/urlhandler.ashx
       # Generating links for:
       #   - old version of agent console: /VisitorMonitor.aspx
       #   - beta servey page: /BetaSurveyHandler.ashx
       #   - feedback page:: /FeedbackHandler.ashx
       livechat-portal: https://hosted.comm100.com/livechat
       # Link shown on login page for restoring password
       forgot-password: https://www.comm100.com/secure/ForgotPassword.aspx
     # Email address used when trial expired:
     #   `If you want to extend the trial period, please contact ${sales-email}.`
     sales-email: sales@comm100.com
     # When set to true, application will force to use https, check `getUrl` in utils/helper.js
     # By default, it should be `true`
     # Currently, only mock server will set this to `false`,
     # check build/server.dev.js and build/webpack.config.js for details
     isSSL: true
     # Following are config for building electron installer, which are different under brandings
     # configs that are shared amoung brandings are defined in build/electron.build.js
     electron:
       dmg:
         background: ./build/dmg-background.comm100.png
       publish:
         url: https://hosted.comm100.com/livechat/electron/
       productName: *comm100-branding
       appId: com.Comm100.LiveChat
       copyright: Copyright © 2017 Comm100 Network Corporation
       artifactName: Comm100LiveChat-Setup-${os}.${ext}
       win:
         icon: ./build/icon.comm100.ico
       nsis:
         artifactName: Comm100LiveChat-Setup-${os}.${ext}
       mac:
         icon: ./build/icon.comm100.icns
       directories:
         output: release-comm100
         buildResources: release-comm100
     # Following are for generating config.json, which is used in app/main.js
     app:
       # Main entry point looking for index.html
       mainEntry: https://route.comm100.com/routeserver/electron/
       # Backup entry point looking for index.html when main entry access failed
       # Check app/index.html for details
       backupEntry: https://route1.comm100.com/routeserver/electron/
       # Used to generating `${product} Agent Console` as default window title
       # Check app/index.js and app/business/TrayManager.js
       # Notice: when page loading finished, title will be controlled by JavaScript loaded in page
       product: *comm100-branding
       # prefix to icon images that are different under brandings
       # these image files can be found in app/icons
       # code can be found in app/utils/helper.js
       # prefix is used instead of suffix because electron will recognize @2x, @3x suffix in image
       # and use these different resolution images under different screens
       prefix: *comm100-name
     # Following are for genrating package.json, which is used in app folder (i.e. electron app)
     # Default config of package.json is defined in app/package.json
     # Here only defines what differs between brandings
     package:
       # File description in installer, check Property > Details > File description
       description: Comm100 Live Chat
       name: Comm100LiveChat
       author: Comm100 Network Corporation
   ```

   ​

2. branding/config.yaml 增加 Comm100AgentConsoleAPI 和 Comm100LiveChat 变量名称定义

3. 修改 sdk 和 webpack 编译的配置，根据 branding/config.yaml 的配置，用 replace 修改代码

4. 根据 state.config.settings.siteInfo.ifEnableLiveChatAPI 开启 extension 功能（关闭时删除 Comm100LiveChat 变量定义并在 store/extensionsManager 中不执行任何操作）

5. 根据 state.config.settings.siteInfo.ifEnableTeamWork 开启 Kickoff/Set Away 按钮

6. 根据 state.config.settings.siteInfo.ifEnableChatdvancedFunc 开启发送文件功能，包括：Chat Action > Icon，Drag & Drop 功能，TextEditor 中 Ctrl+v 功能

7. Cancel Account 和 Login Error link 不配置时不显示链接


# Portal

1. 增加 `EnumFeatureStatus.cs` 枚举

   ```c#
   public enum FeatureStatus {
     enable,     // feature enabled
     upgradable, // feature upgradable
     available,  // feature available, but requires manual upgrade (business => enterprise)
     disallow,   // feature not available
   }
   ```

   ​

2. 增加 `FeaturePoints.cs` 对象

   ```c#
   public class FeaturePoints {
     public FeaturePoints(Int32 siteId) { }
     
     public Boolean ifEnable(FeaturePoint fp) { }
     public FeatureStatus getStatus(FeaturePoint fp) {
       if (ifEnable(fp)) return FeatureStatus.enable;
       // ...
       
     }
   }
   ```

3. 在 `BackMasterPage.Master.cs` 中

   1. 增加 `FeaturePoints` 对象
   2. 增加页面的配置信息
   3. 在 `Page_Load` 中根据 `FeaturePoints` 对象的数据和页面的配置信息生成目录
   4. 提供 `InitPage` 方法替换 `SetMasterMenuStyle` 方法，除根据 `EnumMasterMenuType` 设置目录的样式外，根据配置选择是否显示页面，或在没有权限的情况下跳转

4. 在 `EditionUpgrade.ascx` 页面中提供前端接口

   1. 根据页面中的 `promotion` 全局变量定义的设置选择是否显示升级内容
      1. 依次遍历 `promotion` 数组的定义，找到第一个 `feature` 没有开启的设置；若没有找到，结束
      2. 将 `content` 内容渲染成 HTML。其中，根据 `feature` 的状态，渲染升级按钮和链接：
         + 若 `feature` 是 `upgradable` 状态，文字是 upgrade；若 `feature` 是 `available` 状态，文字是 chat with agent
         + 若 `feature` 是 `upgradable` 状态，点击是展示模态层（Comm100）或跳转到指定页面（LiveChat100）；若 `feature` 是 `available` 状态，点击是弹出聊天窗口
         + 点击的功能差异通过修改 `editionUpgrade.show_upgrade` 函数完成：如果存在 upgrade link，则跳转，否则跳出弹出层。Comm100 不配 upgrade link，其他平台配置。配置在 `BrandingConfig.cs` 中增加。
         + ​
      3. 将渲染好的 HTML 替换 `container` 下的内容
   2. 提供查询某功能是否开启的接口

5. 在各个页面中

   1. 用 `((BackMasterPage)(Master)).InitPage(xxx)` 替换原来的 `((BackMasterPage)(Master)).SetMasterMenuStyle(xxx)` 方法

   2. 在页面中定义

      ```javascript
      var promotion = [
        { feature: 'xxx', content: 'xxx', container: 'xxx' },
        // ...
      ];
      ```

   3. 根据 `((BackMasterPage)(Master)).FeaturePoints` 对象的数据选择是否接受用户的改动