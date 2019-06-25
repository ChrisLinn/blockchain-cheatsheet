# Crypyto Coding

总结自: https://github.com/veorq/cryptocoding

+ sa


---

另外 [<<白帽子讲 web 安全>>](https://book.douban.com/subject/10546925/) 中提到：

+ 不要使用 ECB 模式
    * ~~会带有明文的统计特征~~
    * ~~尤其当 要加密的明文多于一个分组的长度时~~
+ 不要使用流密码（比如 RC4）
+ 使用 HMAC-SHA1 代替 MD5（甚至是代替 SHA1）
+ 不要多次使用同一个密钥进行加解密
+ salts 和 IV 需要随机产生
    * rand() 不一定随机，或者范围太小
    * ~~CBC 模式的 padding oracle attack~~
+ 密钥管理
    * 密码系统的安全性 应该 依赖于 密钥的复杂性而不是算法的保密性， 选择 足够安全的加密算法其实不难
    * 对于web应用来说，常见做法是将密钥/密码保存在配置文件或者数据库中，在使用时 有程序读出密钥并加载进内存，密钥所在的配置文件或数据库需要严格的控制访问权限
        - 或可将  所有密钥&配置敏感文件集中保存在一个服务器（集群）上，并通过 web service 的方式 提供 获取密钥的 api。每个web 应用在需要使用密钥时通过带认证信息的 api 请求密钥管理系统，动态获取密钥
    * 生产测试环境使用的密钥应不同
    * 定期更换密钥
+ 不要自己实现加密算法，尽量使用安全专家已经实现好的库
+ 不要依赖系统的保密性


当你不知道如何选择时，建议:

+ 使用 CBC 模式 的 AES256 用于加密
+ 使用 HMAC-SHA512 用于完整性验证
+ 使用带有 salt 的SHA-256 或 SHA-512 用于 hashing

---

- https://bloodzer0.github.io/ossa/application-security/sdl/go-scp/
    + go语言安全编码规范（中文版）