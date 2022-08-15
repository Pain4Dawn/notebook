# OpenResty 
基于lua开发的nginx的插件，提高性能。
# 架构场景
+ LVS作为负载均衡 基于IP转发
+ nginx 作为反向代理服务器，4层可以基于url转发
+ server 暴漏服务
+ DNS
+ CDN
+ SLB 阿里的负载均衡
+ keepalived：高可用
# LVS
基于IP的负载均衡，快速转发
## 名词
+ DS：指前端负载均衡节点
+ RS：后端真实服务器
+ VIP：向外部直接面向用户的请求
+ DIP：用于和内部主机通讯的IP
+ RIP：后端真实服务器IP
+ CIP：客户端IP
## 四种模式
### DR模式
将目的MAC地址改为后端realserver的mac地址，走二层协议，访问真实客户端，然后后端根据cip直接发包给客户端。
### NAT模式
使用地址转发，在PREROUTING链上转发给指定REALIP（到vip后，会修改源IP和源MAC）。返回时，会经过LVS转发过去
### FULLNAT模式
### TUN模式
包两层IP信息，进行IP转发，后返回时拆包直接返回客户端
## LVS算法
+ 最少链接lc
+ 轮询调度rr
+ 目标地址散列

+ [LVS原理](https://blog.csdn.net/lcl_xiaowugui/article/details/81701949)
+ [LVS应用场景](http://www.yunweipai.com/35579.html)
+ [LVS原理](https://xie.infoq.cn/article/ceb500f2695da7121a1cc2b66)
+ [keepalived 高可用](https://dudashuang.com/load-blance/)
