# keytool

keytool 指令

Keytool 是一个Java 数据证书的管理工具 ,Keytool 将密钥（key）和证书（certificates）存在一个称为keystore的文件中。

主要流程

+ 创建带公钥的keystore
+ 导出证书
+ 导入汇总的keystore中
+ 总的keystore发给需要认证的人

具体指令：

```
# 生成带公钥的keystore
keytool -genkey -alias jude -keypass 123456 -keyalg RSA -keysize 1024 -validity 365 -keystore ./yushan.keystore -storepass 1234 -dname "CN=jude, OU=360, O=360, L=SY, ST=LN, C=CN"
# 查看keystore
keytool -list -v -keystore ./yushan.keystore 12345678
# 导出证书
keytool -export -alias jude -keystore ./yushan.keystore -file ./yushan.crt -storepass 12345678
# 打印证书
keytool -printcerat -file yushan.crt
# 导入公用的keystore
keytool -import -alias shuany -file ./shuany.crt -keystore ./yushan.keystore -storepass 12345678
```

