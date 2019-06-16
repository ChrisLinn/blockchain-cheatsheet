# Crypyto Coding

总结自: https://github.com/veorq/cryptocoding

+ sa


---

另外 [<<白帽子讲 web 安全>>](https://book.douban.com/subject/10546925/) 中提到：

+ 不要使用 ECB 模式
+ 不要使用流密码（比如 RC4）
+ 使用 HMAC-SHA1 代替 MD5（甚至是代替 SHA1）
+ 不要多次使用同一个密钥进行加解密
+ salts 和 IV 需要随机产生
    * rand() 不一定随机，或者范围太小
+ 密钥管理