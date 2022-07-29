# zookeeper SSL

SSL 认证，线下密钥对用于验证签名正确性，线上密钥对用于解密数据

zookeeper的SSL加密原理

确认发送keystore.jks 和keystore.jks的密码。用于验证起名正确性。

根据perm公钥来解密

Add this to the zkEnv.sh

```
export SERVER_JVMFLAGS="
-Dzookeeper.serverCnxnFactory=org.apache.zookeeper.server.NettyServerCnxnFactory
-Dzookeeper.ssl.keyStore.location=/root/zookeeper/ssl/testKeyStore.jks 
-Dzookeeper.ssl.keyStore.password=testpass 
-Dzookeeper.ssl.trustStore.location=/root/zookeeper/ssl/testTrustStore.jks 
-Dzookeeper.ssl.trustStore.password=testpass"
```

## SSL协议
### 概述
+ 数据签名是解决数据完整性和不可抵赖性
+ https 是对数据进行加密传输，
+ 以上两者都需要一个权威机构对公钥发行者进行背书，这个就是证书的作用。

### https请求流程
+ 客户端发给服务端支持的hash算法
+ 服务端用指定hash算法加密，生成证书发给客户端
+ 客户端对证书验收
+ 客户端把对称密钥用公钥加密发给服务器
+ 服务器解密获得对称密钥

## java证书体系
+ java 利用keytool生成keystore 作为证书发行单位


[java 证书体系](https://www.cnblogs.com/molao-doing/articles/9687445.html)