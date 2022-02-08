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

