# Wallet
    
<!-- 
+ https://medium.com/taipei-ethereum-meetup/%E8%99%9B%E6%93%AC%E8%B2%A8%E5%B9%A3%E9%8C%A2%E5%8C%85-%E5%BE%9E-bip32-bip39-bip44-%E5%88%B0-ethereum-hd-%EF%BD%97allet-a40b1c87c1f7
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://www.jianshu.com/p/54a2b14dfdf2
+ https://github.com/ConsenSys
+ https://zhuanlan.zhihu.com/p/34184347
+ https://juejin.im/post/5ab70c146fb9a028d664203f
+ https://stevenocean.github.io/2018/09/23/generate-hd-wallet-by-bip39.html
+ https://chain.com/docs/1.2/protocol/specifications/chainkd
+ https://juejin.im/post/5b5548fff265da0f9313a5c8
 -->

## 入门

虚拟货币钱包实际上是管理和储存 key 的工具。key 就是私钥，会有对应的公钥, 而帐户从公钥衍伸出来。

+ 私钥 = 助记词 = keystore + 密码
+ 助记词生成 seed -> 生成私钥。
    * 助记词可以恢复私钥。
    * 私钥不能导出助记词，因为有个 hash 不可逆。

Bitcoin 中有两种主要类型的钱包

+ 非确定性钱包（Nodeterministic Wallet）：该钱包中的每个密钥都是从不同的随机数独立生成的，密钥彼此之间没有任何关系，这种钱包也被称为 JBOK 钱包（Just a Bunch Of Keys）
+ 确定性钱包（Deterministic Wallet）：其中所有的密钥都是从一个主密钥派生出来的，这个主密钥就是种子（seed），在该类型的钱包中，所有的密钥之间都是相互关联的，如果有原始种子，就可以再次生成全部的密钥；在确定性钱包中，可以使用不同的密钥推导方式。目前最常用的推导方法是 树状结构，也称为 分层确定性钱包 或 HD 钱包；


BIP32, BIP39, BIP44 共同定義了目前被廣泛使用的 HD Wallet

## BIP32
定义 Hierarchical Deterministic wallet (HD Wallet).

可以从单一个 seed 产生一树状结构储存多组 keypairs（私钥和公钥）。

可以方便的备份、转移到其他相容装置（因为都只需要 seed），以及分层的权限控制等。

![derivation](/img/derivation.png)

## BIP39
mnemonic code(phrase) 助记词或助记码。

将 seed 用方便记忆和书写的单词(一般 12 个)表示。


## BIP43
多用途 HD 钱包结构


## BIP44
基于 BIP32 的系统，赋予树状结构中的各层特殊的意义。让同一个 seed 可以支援多币种、多帐户等。

各层定义：
```
m / purpose' / coin_type' / account' / change / address_index
```

+ purporse': 固定值 44', 代表 BIP44
+ coin_type': 代表币种
    * BTC 是 0'
    * ETH 是 60'
