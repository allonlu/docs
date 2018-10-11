
# 独立部署使用OneMaximumOn

1. 将应用程序升级到OneMaxiumOn或以后版本
2. 确认以下信息在OneMaximumOn Server中唯一
  - Platform Id, 如果跟别的Platform Id重复, 则需要修改该PlatformId
  - SiteId, 如果别的平台中也有该站点Id, 则需要先关闭别的平台该站点的Maxon功能，避免同步, 同时删除OneMaximumOn Server中的该站点数据
3. 修改RouteServer中的副RouteServer地址到sharedmax.comm100.com
4. 