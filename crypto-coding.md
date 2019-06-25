# Crypyto Coding

总结自: https://github.com/veorq/cryptocoding (介绍了一些 side channel attack)

+ 传统方式 比较字符串是否相等，为了快，逐位比较一有不同就返回
    * 但这样可以通过 函数执行的时间来猜测 前多少位相等
    * 防止这种攻击的方式是无论如何（字符串是否相等，多少位相等）都固定时间（每一位都比完）才返回
+ 条件执行语句判断不应和 secret 有关 (avoid branching controlled by secret data)
    * 不同 branch 执行时间不同，那么可利用执行时间来猜测 condition, 从而猜测 secret
        - 比如 RSA 快速幂取余算法, [在密码硬件中使用会被攻击](https://wiki.x10sec.org/crypto/asymmetric/rsa/rsa_side_channel/)
        ```
        int PowerMod(int a, int b, int c)
        {
            int ans = 1;
            a = a % c;
            while(b>0) {
                if(b % 2 == 1) // 当b为奇数时会多执行下面的指令
                    ans = (ans * a) % c;
                b = b/2;
                a = (a * a) % c;
            }
            return ans;
        }
        ```
            + 保护方式是一般会 [加信号干扰](https://www.zhihu.com/question/22325815) , 防止被猜测
        - 再比如 椭圆曲线  中的 [double-and-add point multiplication algorithm](https://crypto.stackexchange.com/questions/48790/wikipedias-double-and-add-point-multiplication-algorithm-for-elliptical-curves)
        ```
        N ← P
        Q ← 0
        for i from 0 to m do
           if d[i] = 1 then
               Q ← point_add(Q, N)
           N ← point_double(N)
        return Q
        ```  
+ 同理 loop bounds 也不应该和 secret 有关
+ table look-ups indexed


---

另外 [<<白帽子讲 web 安全>>](https://book.douban.com/subject/10546925/) 中提到：

+ 不要使用 ECB 模式
    * (会带有明文的统计特征)
    * (尤其当 要加密的明文多于一个分组的长度时)
+ 不要使用流密码（比如 RC4）
+ 使用 HMAC-SHA1 代替 MD5（甚至是代替 SHA1）
+ 不要多次使用同一个密钥进行加解密
+ salts 和 IV 需要随机产生
    * rand() 不一定随机，或者范围太小
    * (CBC 模式的 padding oracle attack)
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