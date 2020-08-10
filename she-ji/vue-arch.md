# 访客端代码高可用

1. 访客端代码采用双地址从不同的域名地址上下载js，可以保证有一个地址无法响应的情况下可以从另一个地址下载到代码
  - https://vue.comm100.com/livechat.ashx?siteId={site_id}
  - https://vue1.comm100.com/livechat.ashx?siteId={site_id}

2. vue本身没有状态，不依赖于数据库，而是根据route server中的站点平台对应关系，返回真正的chat server地址给访客端，另外具体逻辑的JS代码也从这里返回

  - vue和vue1本身可以做active-active的load-balance部署
    - 可以将vue配置到cloudflare的loadbalance下，在全球的各个地区aws服务器上分别部署程序
      - 北美， 亚太， 欧洲三个节点
    - vue1部署到自己机房下面， 需要考虑使用其他的防火墙公司，不用cloudflare

  - route server的对应关系目前在本地会有缓存，可以增加这个缓存的持久化功能，避免两个Route Server都宕机以后可能无法正常响应
  
3. route server为主副的部署结构，只允许在主Route Server写入数据，同时会同步数据到副Route Server
  - Route 部署在自己机房中
  - Route1 部署在AWS上

  