# 基础控件
1. value/onChange等属性都在当前一级，而不要放到input下面
2. 尽量不公开样式让开发者使用，而是使用属性来控制
3. 命名要一致，如方向一律使用layout属性(horizontal, vertical)
4. 多项选择的项统一使用options属性
5. 当前控件的值使用value属性，而不使用selectedValue之类的其他属性
6. enable/disabled属性统一采用disabled
7. 如果要公开class，则采用className
8. change事件采用onChange属性


# 源码管理
1. 公共控件的组件只有公共控件组的成员可以提交代码到master分支
2. 其他项目组需要修改或者添加新的组件可以创建一个分支，在自己的分支中添加或者修改，然后创建一个Pull Request，通知公共控件组成员Review后Merge到master中