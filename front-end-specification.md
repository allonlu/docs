
# 公共基础dll库的维护

  dll库为前端项目使用的第三方库(包括react, redux及其相关的第三方库)打包而成, 供agent-console, comm100-react-component, tickets, social等前端项目共用,在前端项目构建中, 因不同模块都需要使用到这个库, 因此这部分的变更必须受到有效的控制。dll库的改动只会发生在项目引用的第三方库发生变化时(一般为升级使用的版本)，一般在我们目前使用的稳定版本中没有问题不会升级，因此这个变更不会很频繁。

## 变更控制

1. 如某个项目需要变更这部分内容需要邮件提出申请, 表述原因及升级的影响，通知所有的前端人员进行讨论, 抄送到所有的开发人员及项目经理
2. 经过讨论确认变更以后才能进行第三方库的变更, 如果这个变化需要发布Agent Console, 那么相对应的运行在Agent Console里面的Ticket和Social等需要重新编译

# 公共组件的使用和维护

  目前因为产品这边针对几个模块的样式没有统一, 因此公共组件库需要针对两个样式维护两个版本, 即组件库中的两个分支, 分别为针对agent-console样式的组件库, 以及针对后台样式的组件库。原则上所有项目需要用到的组件如果在组件库中已经有的都直接使用组件库中的组件, 而不是重新开发一个, 如果组件库中的组件存在问题, 或者目前的接口不能满足新的需求需要对组件进行修改的话则由下面分配的组件库的维护负责人来进行。

1. Agent-Console样式
  - 维护负责人: Andy
  - 使用的模块: Ticket, Social
2. Portal样式
  - 维护负责人: Eric
  - 使用的模块: Bot, Partner

# 代码库的分支合并管理

## 特定分支说明
1. Master

  Master分支作为主分支存在, 这个分支不做具体的开发, 只做合并。项目行进到IT集成以后需要拉取Master分支合并到自己的版本, 因为在IT过程中可能会有其他项目合并到Master中, 因此需要在这个阶段不定时的拉取Master中的版本合并进自己的版本， 项目行进到RC以后需要将自己的版本合并进Master分支(此时的合并需要保证已经将Master的版本合并到自己的版本中并且进行过一轮测试)进行编译发布。

  - 特殊说明： 因为Master的特殊性, 在某一个项目处于RC阶段时, 其他项目无法合并进Master进入RC, 因此这个时候如果有紧急的补丁版本需要发布, 允许直接在分支中编译发布, 但是要确保发布以后合并进Master和Release, 并且RC状态的项目在合并进新的分支以后需要重新RC该模块或者在发布中增加一个该模块的补丁包。

2. Release

  Release分支为长期维护一个稳定的版本, 只有真正发布以后才允许合并到Release分支, 因此所有项目开始时都可以从Release进行Branch, 发布以后的项目

3. 其他分支

  其他分支作为项目开发过程中的分支, 原则上都从Release进行分支, 如有特殊情况可以从Master上分支。

## Tag管理

每个发布合并进Release以后都需要打上Tag, 以方便针对独立部署可以较方便的找到历史版本。
