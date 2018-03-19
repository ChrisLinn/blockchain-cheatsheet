# Bitcoin
+ 比特币结合 4 个创新
    * 去中心化的对等网络（比特币协议）
    * 公共交易总帐（区块链）
    * 独立交易确认和货币发行的一套规则（共识规则）
    * 实现有效的区块链全球去中心化共识的机制（工作量证明算法）


## 一些历史
+ 非对称加密
    * 1976: RSA
    * 1985： ECC 椭圆曲线函数加密
+ P2P
    * 1969: 首次提出
    * 1999: Shawn Fanning 创造全球第一个走红的P2P共享平台 Napster
+ PoW
    * 1997: Adam Back 发明 HashCash 哈希现金
        - 最初创造 HashCash 的目的是对抗 DoS
    * 2004: Hal Finney 创造出了第一个可重复使用的工作量证明系统 [Reusable proof of work system](http://en.wikipedia.org/wiki/Proof-of-work_system)
        - 比特币史上的第一笔交易就是发生在中本聪与哈尔芬尼之间，是第一位得到比特币的人。
+ Bitcoin
    * 2008, Satoshi Nakamoto, “Bitcoin：A Peer-to-Peer  Electronic  Cash    System”
    * 2009, 比特币网络


## 设计思想
+ 比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。
+ 为什么 block 设计为 1M？
+ 为什么总体供给是 2100w？
+ 为什么 10 分钟出一个区块？为什么 6 个区块之后就可以认为之前生成的区块被确认了？
+ 为什么用 base58？为什么不用我们熟知的 base64？
+ 为什么交易速度如此之慢，只有若干 tx / s？
+ 为什么 PoW？现有的方法为什么不可用？（呃，现有有什么方法？）
+ 为什么给矿工生成看似无意义的空区块的空间？

+ 为什么钱包的地址要 hash 一遍，而不是直接用 public key 的 base58?


+ 为什么需要 merkle tree？

+ 为什么用 ECDSA 而不是 RSA？

+ 为什么 double hash？

+ 为什么用 little endian，而不是 big endian（network order）？


## P2P
+ [P2P网络-比特币开发指南](https://www.jianshu.com/p/a737d79501f2)
+ [比特币网络架构及节点发现分析](http://www.cnblogs.com/mazhiyong/p/8431455.html)
+ bitcoin 的 p2p 怎么实现
    * `chainparams.cpp` 代码中写死了几个域名，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
    * 可防 DDOS，有黑名单列表
+ 全节点
+ SPV 轻节点

## Transaction
+ 比特币有哪几种交易类型？
    * Generation（挖矿）, Pay to Pubkey Hash, Pay to Script Hash 

## UXTO
+ 为什么设计 UTXO？


## 地址
+ 比特币地址的生成过程是？ 
    * 第一步，随机选取一个 32 字节的数、大小介于 1 ~ 0xFFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFE BAAE DCE6 AF48 A03B BFD2 5E8C D036 4141 之间，作为私钥。 
    * 第二步，使用椭圆曲线加密算法（ECDSA-secp256k1）计算私钥所对应的非压缩公钥。  
    * 第三步，计算公钥的 SHA-256 哈希值。
    * 第四步，取上一步结果，计算 RIPEMD-160 哈希值。
    * 第五步，取上一步结果，前面加入地址版本号。
    * 第六步，取上一步结果，计算 SHA-256 哈希值。
    * 第七步，取上一步结果，再计算一下 SHA-256 哈希值。
    * 第八步，取上一步结果的前 4 个字节。
    * 第九步，把这 4 个字节加在第五步的结果后面，作为校验。
    * 第十步，用 base58 表示法变换一下地址。

## mul-sig 多重签名

## Lightning Network
