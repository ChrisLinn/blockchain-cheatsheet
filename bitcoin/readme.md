# Bitcoin

比特币采用SHA-256、RIPEMD-160哈希算法生成密钥，使用SHA-256作nonce碰撞以挖矿。


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
+ bitcoin 的 p2p 怎么实现
    * [代码中写死了几个域名](???)，通过 DNS 得到 __种子节点__ IP列表
        - 另外亦可通过 种子文件/命令行传入其他节点ip 
    * 默认连接8个节点，允许最多125个
+ 全节点
+ SPV 轻节点

## UXTO

+ 为什么设计 UTXO？

## mul-sig 多重签名

## Lightning Network
