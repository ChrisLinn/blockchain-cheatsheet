# Blockchain

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

## Bitcoin 区块文件结构
__Block__

| Size | Field | Description|
| - | - | - |
| 4 bytes | Block Size | The size of the block, in bytes, following this field. |
| 80 bytes | Block Header | Metadata for the block. |
| 1-9 bytes (VarInt) | Transaction Counter | How many transactions follow. 平均每个区块至少包含超过 500 个交易。 |
| Variable | Transations | The transavtions recorded in this block. 平均每个交易至少是 250 Bytes. |

__Block Header__

| Size | Field | Description |
| - | - | - |
| 4 bytes | Version | A version number to track software/protocol upgrades. |
| 32 bytes | Previous Hash | A reference to the hash of the previous (parent) block in the chain. |
| 32 bytes | Merkle Root | A hash of the mekle tree of this block's transactions |
| 4 bytes | Timestamp | The approximate creation time of this block (seconds from Unix Epoch) |
| 4 bytes | [Difficulty Target](#pow-proof-of-work) | The Proof-of-Work algorithm difficulty target for this block |
| 4 bytes | [Nounce](#pow-proof-of-work) | A counter used for the Proof-of-Work Algorithm |


+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中
+ 

### [Coinbase](https://en.bitcoin.it/wiki/Coinbase)
挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)


## GHOST rule
+ __GHOST (Greedy Heaviest-Observed Sub-Tree) rule/protocol__
    + Sompolinsky, Y., and Zohar, A. Accelerating Bitcoin’s transaction processing. fast money grows on trees, not chains. In Financial Cryptography (Puerto Rico, 2015).
    + Yonatan Sompolinsky and Aviv Zohar. Secure high-rate transaction processing in Bitcoin. In Financial Cryptography and Data Security - 19th International Conference, FC 2015, pages 507–527, 2015.
+ [The Quest for Scalable Blockchain Fabric: Proof-of-Work vs. BFT Replication](https://allquantor.at/blockchainbib/pdf/vukolic2015quest.pdf)
    * 通过区块子树的权重，而非给定区块的最长子链，解决 pow 区块链冲突。 尽管 GHOST 本质上只是一个冲突解决策略，但它提供了比比特币的标准最长（最重）链规则更好的性能：它提供了更安全地增加区块频率和区块大小的方法。因为它更加公正，而且更加充分地利用了算力。
    * ETH也使用了一个 GHOST的变种，尽管 GHOS-POW 的性能还没有被充分地用高负荷压测。
+ [Bitcoin-NG: A Scalable Blockchain Protocol](https://arxiv.org/pdf/1510.02037.pdf)
    * GHOST 升级版
    * bitcoin 原本的规则可以保证在任何时候至少有一个节点知道主链是什麽——因为它知道所有的区块。GHOST 则无法保证这一点。(虽然通过广播所有的区块可以解决，但是这又带来 DoS 的风险——恶意节点狂发低难度的区块。这种情况下，GHOST表现得比原生协议要差，因为广播区块开销超过了选链规则带来的好处等到GHOST有实用解决方案，比如说广播区块头而非整个块，可以用来补足 bitcoin-NG)
    * GHOST 中，被剪枝的子树上的区块只在分岔点影响链选择。Bitcoin-NG 在高带宽和吞吐的情况下分叉很小，使得算力更加充分被利用，选链更加公平。


## 公有链 vs 联盟链 vs 私有链

## 拜占庭将军问题

## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

+ 下面哪种共识机制效率最低？（__A__）
    * __A.__ PoW __B.__ Pos __C.__ DPoS  __D.__ PBFT

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

```
区块收益=(币龄/365)*年化收益率
币龄=持币量*持币时间, 当发现一个区块，币龄清零。

每个币每天产生1币龄，如持有100个币，总共持有30天，那么币龄就为3000，如果发现了一个POS区块，你的币龄就会被清空为0。每被清空365币龄，将从区块中获得XXX个币的利息(可理解为年利率)
```


例子:

+ 宇宙币CosmosCoin
+ 将来的以太坊ETH
+ Peercoin
    * Coin age-based selection
        - Staking generally requires those that are staking to __lock up their coins for some period of time__ (i.e. can't spend the coins) for a staker to have a chance of __being selected__ to produce a block and collect the block reward.
+ Nxt
    * Randomized block selection
+ BlackCoin
    * Randomized block selection

POS的设计理念：

+ 比特币的区块产量每4年会减半，产量越来越低，大家挖矿的动力不断下降，矿工人数越来越少，整个比特币网络有可能会逐渐陷入瘫痪(因为大家都减少了运行比特币客户端的时间，因此越来越难找到一个P2P节点去连接和同步网络数据)。
    * POS解决方案：在POS体系中，只有打开钱包客户端程序，才能发现POS区块，才会获得利息，这促使很多不想挖矿的人，也会常常打开自己的钱包客户端，这帮助了P2P货币网络的健壮。
+ PoW 很有可能被一些高算力的人、或团队、或矿池，进行51%攻击，导致整个网络崩溃。
    * POS解决方案：在POS体系中，即使你拥有了全球51%的算力，也未必能够进行51%攻击。
+ 比特币其实是一个通货紧缩的体系。因为重装系统或忘记钱包密钥时，永远无法再拿回钱包里的钱，这意味着，每年都会有一些比特币随着钱包的丢失而永远被锁定，这就形成了实质上的通货紧缩。
    * POS解决方案：提供一定的年利率，尽可能保证既不通货膨胀，也不通货紧缩。

在PoS对PoW进行改进后，又引进了新的问题 :

+ 不公平的话语权, 
    * 和股票很像，持币越多的话语权也越多，只要持币就可以不断获得新币（相当于利息），不需要付出其他任何成本
+ 无法抵抗硬分叉
    * POS币如果一旦发生硬分叉，所有持币人都会在两条链上持有相同数量的币，因为POS币挖矿不需要任何成本，所有持币人都会在新的分支上获得收益（不像POW币的矿工只能挖其中一条链），这就会很容易导致大多数持币人都默许分叉的存在。分叉出来的链很容易存活
+ [N@S (Nothing at stake) Attack](./attack.md#ns-nothing-at-stake-attack)
+ [Long-Range Attack](./attack.md#long-range-attack)

### PoW + PoS
POS问题，有人持有了大量币种，自动产生源源不断的利息，马太效应将使得大户越来越有钱

PoW+PoS机制共同作用的数字货币，其工作原理是， __除了通过持币产生利息外，还能通过POW的方式挖矿获得新币__. 采用POS+POW机制后，难度目标值还和币龄相关, 
```
目标值=F(币龄，难度值）
```
其中目标值和币龄呈负相关关系， __持币时间越久，挖矿难度越低__ ，这样即使是普通电脑也有机会发现新区块并获得奖励，一旦找到新区块，原本的币龄将被归零，保证了整个网络的公平性，避免出现中心化趋势

[采用POS+POW混合机制发行的数字币有](https://zhuanlan.zhihu.com/p/31011138):

+ ~~点点币PPCoin~~
+ ~~雅币YaCoin~~
+ 新星币NovaCoin
+ Ethereum (currently)
+ Dash
+ Stratis


### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。
+ 2014 年，第一批拜占庭容错研究者之一的 Jae Kwon 发表了《Tendermint：非挖矿共识》文章，他文章提到允许一批分布式节点来实现非 PoW 挖矿的共识机制，PoW 挖矿共识也被称为「中本聪共识」。中本聪共识实现了分布式节点的信任共识，但存在很多问题，比如速度很慢、电力浪费等，而 Jae Kwon 提出的 Tendermint 拜占庭容错是第一个引入抵押、验证以及节点轮换的共识算法，对中本聪共识进行了大幅度的改善。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

### DAG

不按区块排序&连接，而按交易连接. 

+ 优点
    * 天然支持并发交易， tps 高
+ 缺点
    * 扩展性不好
        - 数据冗余
        - 节点多时 通信复杂度高
            + 寻找ancestor验证

### Algorand

解决 DPoS 腐败，贿赂，串通后的集权行为。

采用 图灵奖获得者Silvio Micali 的随机加密的方法，在 __每个区块__ 诞生的时候 __随机__ 在公链上选出独立的代表，(每次都随机，所以可以防止贿选) __然后，采用拜占庭协议__(擅长运用于少量节点，高效的共识速度，并具有容错性。) 来对新区块产生共识。


## 主网

## 分叉链，子链，侧链
### 分叉链
基于同一个代码，增减一些功能后单独部署的一个区块链

+ 不保留原用户
+ 保留原用户
    * 分叉之前的钱包都能获得等量的新货币

### 糖果
在项目发展之初，为了获得用户/人气等等免费发福利

### 子链
子链指的是在主链的平台来派生出来的具有其他功能的区块链。这些子链不能单独存在，必须通过主链提供的基础设施才能运行，并且免费获得主链的全部用户。一个简单的例子是以太坊上面的 __erc20__  合约。这个合约可以看成是一个逻辑子链，但是这个子链的共识方式与主链一致。

### 侧链
侧链指的是与主链相平行的单独一个区块链，但是它和主链之间可以通过相互了解的协议互联。主链的货币可以通过在主链的可验证的锁定，在侧链获得对应的货币，反之亦可。作为主链的补充，侧链可以提供一些主链不能提供的功能。但是这个互联对共识机制有要求，而且侧链必须有与主链相当的算力才能保证侧链货币的安全性。要达到相当的算力，其实完全就成了另外一个单独的链。

### 跨链
+ https://36kr.com/p/5117175.html
+ https://www.leiphone.com/news/201802/uKPLdIHKRhPgqfEK.html
+ 分布式交易所?

__两个不同账本上价值的流通。__ 从业务角度看，跨链技术就是一个交易所，本质上和货币兑换是一样的。

交易所开展的不同类型数字货币之间的兑换，就是一种跨链价值转移的实现。
同时，交易所利用自身的信用来提供兑换交易时信任的传递。(交易双方都转给交易所代持)

交易所是否能够信任？引入新技术：

+ 公证人机制（Notary schemes）
+ 侧链/中继（Sidechains/relays）
+ 哈希锁定（Hash-locking）
+ 分布式私钥控制（Distributed private key control）


### 双向锚定


## 链池


## 多链技术

### PolkaDot

### COSMOS

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。

### Interledger

## SegWit 隔离见证

## Projects

### [Chain](https://github.com/chain/chain)
企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

### OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。


### Cardano 卡尔达诺
+ 智能合约平台
    * 使用第三代基于 EVM 的虚拟机 [IELE](https://iohk.io/blog/iele-a-new-virtual-machine-for-the-blockchain) ，通过侧链来实现跨链功能
+ 以可证的权益证明共识（provable PoS）[Ouroboros 乌洛波罗斯](#ouroboros-%E4%B9%8C%E6%B4%9B%E6%B3%A2%E7%BD%97%E6%96%AF) 为中心
    * 集比特币（Bitcoin），以太坊（Ethereum，智能合约），波卡（Polkadot，跨链）之大成。
+ 目前第一个通过学术界同行评审的区块链项目
+ 由 Haskell 语言实现，被认为最安全的编程语言之一
+ 代币（token）: ADA
+ 背后主要组织
    * IOHK
    * 卡尔达诺基金会(创立于瑞士, 非营利组织)
    * Emurgo

### HyperLedger
#### Fabric
+ 简述 fabric 中 channel、org、peer 的关系？ 
+ fabric 中的智能合约是如何被加载运行的？
+ fabric 中有哪几类证书类型各有什么作用？ 


### BigchainDB
+ 一款企业级解决方案
+ 暂不支持智能合约，但有 condition-fulfillments 机制
+ 使用成熟的分布式数据库 RethinkDB/MongoDB
    * 每个节点存储本地 MongoDB 数据库。
    * v1.0
        - 多节点共识原本使用 RethinkDB/MongoDB 集群之间节点的同步来做, 不能 BFT
    * v2.0
        - 节点间的通信采用 Tendermint 协议，支持 BFT
+ Decentralize
    * v1.0
        - 中心化严重 (单点控制, 单点故障...)
            + 有且仅有一个主节点进行所有的写入工作，其他节点只是同步主节点写入的内容。
            + 在底层架构设计中仅有一个logical databse，admin权限可删除整个数据库。
    * v2.0
        - 禁止使用 BigchainDB 的 API 对以存储的数据的改变或擦除
        - 每一个节点都在一个独立的 MongoDB 数据库里有完全复制品, 不受数据篡改影响, 并可追查数据篡改