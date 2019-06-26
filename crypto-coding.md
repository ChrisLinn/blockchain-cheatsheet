# Crypto Coding

总结自: https://github.com/veorq/cryptocoding (介绍了一些 side channel attack)

+ 传统方式 比较字符串是否相等，为了快，逐位比较一有不同就返回
    * 但这样可以通过 函数执行的时间来猜测 前多少位相等
    * 防止这种攻击的方式是无论如何（字符串是否相等，多少位相等）都固定时间（每一位都比完）才返回
+ 条件执行语句判断不应和 secret 有关 (avoid branching controlled by secret data)
    * 不同 branch 执行时间不同，那么可利用执行时间来猜测 condition, 从而猜测 secret
        - 比如 RSA 快速幂取余算法, [在密码硬件中使用会被攻击](https://wiki.x10sec.org/crypto/asymmetric/rsa/rsa_side_channel/)
        ```c
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
+ avoids table look-ups indexed by secret data
    + 没细研究.......
+ 注意编译器是否优化掉了不该优化的代码, 比如本来要清除内存中的secret，结果被编译器一优化，不清除了
    + 解决办法：对比真正生成的汇编代码
+ 记得可靠地(!)清空 secect
    + 不幸的是以下两种语言无法保证一定能清空
        * 带GC的语言
            - Go, Java, JS...
        * 使用immutable strings的语言
            - Swift, Obj-C...
+ 避免 secure 和 insecure 的 API 的混淆
    + 不同平台函数的安全性不一样
        * 不要假设平台安全
        * 如果 override 掉 不安全的 func？
            - 如果 override 掉 不安全的 func？
                + override 失败则仍会跑不安全的 func
                + 移植到别的平台后可能会跑不安全的 func
+ Avoid mixing security and abstraction levels of cryptographic primitives in the same API layer
    + 没细研究.......
+ 应该用 unsigned bytes 来表示 binary data (bytestrings)
    + C 中 char 类型的正负是 implementation-defined 的, 那么比如对于这段代码
    ```c
    int decrypt_data(const char *key, char *bytes, size_t len);

    void fn(...) {
        //...
        char *name;
        char buf[257];
        decrypt_data(key, buf, 257);

        int name_len = buf[0];
        name = malloc(name_len + 1);
        memcpy(name, buf+1, name_len);
        name[name_len] = 0;
        //...
    }
    ```
        + 如果 char 是 signed 型的，`buf[0]` 可能为负
            + `malloc` 和 `memcpy` 的范围可能很大
            + `name[name_len] = 0;` 语句存在 heap corruption 的危险
            + 如果 `buf[0]` 为 `255`, `name_len` 则会为 `-1`, 
                + 那么就造成了 allocate a 0-byte buffer, 然后 memcpy (size_t)-1 这么大(过大)的数据进该 buffer 中, 造成 [非法堆内存访问](https://stackoverflow.com/questions/13669329/what-is-a-memory-stomp)
+ 保证强随机性
    + __TBD__
+ Always typecast shifted values
    + __TBD__

<!-- 
    + leaking just one bit of each random number in the DSA will reveal a private key astonishingly quickly. Lack of randomness can be surprisingly hard to diagnose: the Debian random number generator failure in OpenSSL went unnoticed for 2 years, compromising a vast number of keys. The requirements on random numbers for cryptographic purposes are very stringent: most pseudorandom number generators (PRNG) fail to meet them.
    + Bad solutions
    + Solution
-->

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
        + __TBD__

---

+ 亦可参考 [PacktPublishing/Security-with-Go 的 Cryptography 章节代码](https://github.com/PacktPublishing/Security-with-Go/tree/master/Chapter06)
    * [目录](https://www.packtpub.com/networking-and-servers/security-go)