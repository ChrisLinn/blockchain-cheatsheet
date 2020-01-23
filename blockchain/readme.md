# Blockchain

## 搭建简易版区块链
+ golang
    * https://github.com/Jeiwan/blockchain_go
        * https://jeiwan.net/
    * https://github.com/liuchengxu/blockchain-tutorial
    * https://github.com/izqui/blockchain
    - https://github.com/piotrnar/gocoin
    * https://github.com/33cn/chain33
    * https://github.com/crisadamo/gochain
    * https://github.com/kofj/naivechain
    * https://github.com/chaors/PublicBlockChain_go
+ python
    * https://github.com/dvf/blockchain
        * https://hackernoon.com/learn-blockchains-by-building-one-117428612f46
    * https://bigishdata.com/2017/10/17/write-your-own-blockchain-part-1-creating-storing-syncing-displaying-mining-and-proving-work/


## 区块文件结构(Bitcoin)

不同币种的区块链结构可能不同，但 PoW 系的结构基本相似。此处以 Bitcoin 的区块链结构为例。

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


### Coinbase

see [https://en.bitcoin.it/wiki/Coinbase](https://en.bitcoin.it/wiki/Coinbase)

挖矿类型交易(generation)的“输入”的内容。可包含任意信息，比如创始区块中包含的是:
```
The Times 03/Jan/2009 Chancellor on brink of second bailout for banks
```

扩展阅读: [比特币有哪几种交易类型?](/bitcoin/readme.md#transaction)

### transaction commitment, input/output commitment
#### Commitment Schemes
Commitment schemes are a way for one counterparty to commit to a value such that the value committed remains private, but can be revealed at a later time when the committing party divulges a necessary parameter of the commitment process. Strong commitment schemes must be both information hiding and computationally binding.

[Wikipedia](https://en.wikipedia.org/wiki/Commitment_scheme):
A commitment scheme is a cryptographic primitive that allows one to commit to a chosen value (or chosen statement) while keeping it hidden to others, with the ability to reveal the committed value later. Commitment schemes are designed so that a party cannot change the value or statement after they have committed to it: that is, commitment schemes are binding. Commitment schemes have important applications in a number of cryptographic protocols including secure coin flipping, zero-knowledge proofs, and secure computation.

##### Pedersen commitment
+ https://github.com/adjoint-io/pedersen-commitment
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/9704/why-is-the-pedersen-commitment-computationally-binding
+ https://crypto.stackexchange.com/questions/40306/pedersen-commitments-and-addition
+ https://crypto.stackexchange.com/questions/54439/why-is-the-pedersen-commitment-perfectly-hiding
+ https://crypto.stackexchange.com/questions/54735/what-are-the-pros-and-cons-of-pedersen-commitments-vs-hash-based-commitments
+ https://crypto.stackexchange.com/questions/64437/what-is-a-pedersen-commitment

The Pedersen commitment sheme allows a sender to create a commitment to a secret value. They may then later open the commitment and reveal the value in a verifiable manner that binds them to their commitment. 

A commitment shceme consists of a three stages:

+ Setup
+ Commit
+ Open

Pedersen commitment scheme has the following properties:

+ Hiding: A dishonest party cannot discover the honest party's value.
+ Binding: A dishonest party cannot open his or her commitment in more than one way.
+ Non-correlation: A dishonest party cannot commit to a value that is in some significant way correlated to the honest party's value.


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

### pruning
once your node has downloaded the block data and validated the blocks/transactions in it, it throws away the old data that it no longer needs (since it's already been validated).


## transaction 'pinning'?
Transaction pinning happens when:

+ I broadcast a transaction that signals opt-in RBF
+ the transaction does not get confirmed because the feerate is too low
+ someone else broadcasts a new (child) transaction spending one of the outputs of my transaction
+ I now can't bump the fee on the transaction unless I include a fee greater than that of the combined original transaction + the child transaction (BIP 125, rule 3).

If the child transaction in (3) is large (eg a commercial service sweeping up lots of transaction outputs), then the total fee that __I'd need to pay for a valid RBF would be very large__.

In this scenario, my original transaction has been 'pinned' by the child transaction.

Russell O'Connor has proposed [changing the RBF policy rules](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-February/015717.html) to alleviate this problem.


## 公有链 vs 联盟链 vs 私有链

### 联盟链
+ https://github.com/hyperledger
+ https://github.com/libra/libra
+ https://github.com/EYBlockchain
+ https://github.com/jpmorganchase/quorum


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


### ProgPow

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
    * 并发的交易在做共识的时候 网络分区了怎么办


papers:

+ Fairness and Efficiency in DAG-based Cryptocurrencies
    * https://arxiv.org/pdf/1910.02059.pdf
        - do not analyse the security, but the chain quality (fairness) and the tx latency (efficiency)
+ Cryptanalysis of Curl-P and Other Attacks on the IOTA Cryptocurrency
    * https://i.blackhat.com/us-18/Wed-August-8/us-18-Narula-Heilman-Cryptanalysis-of-Curl-P-wp.pdf
        - This one makes IOTA to change their hash algorithm and use a centralised validator

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

<!-- 
+ https://www.preethikasireddy.com/posts/how-does-cosmos-work-part1
    * 这篇文章从技术架构到开发体验介绍了Cosmos，并且与Ethereum等公链进行了对比。
+ [软硬核：彻底读懂 Cosmos 如何一键发链与万链互联](https://mp.weixin.qq.com/s?__biz=MzI1Mzk4ODIwOA==&mid=2247487918&idx=1&sn=c5fc5d9f952611eba4a97cf2f0ed306e&chksm=e9cd4db1debac4a76c6d31100e385439ab65025ec050789e92d5537f80113b43c6b1e9b70959&mpshare=1&scene=24&srcid=0314oSWk6KTcpDqIJK1AVHqP&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
* [How to Build a Blockchain App with Cosmos SDK](https://bbs.chainon.io/d/3339-how-to-build-a-blockchain-app-with-cosmos-sdk)
+ [谈论 COSMOS 时，我们到底在谈论什么？](https://www.chainnews.com/articles/262063844998.htm)
+ [写在跨链项目Cosmos上线前夜](https://mp.weixin.qq.com/s/bViNUPfOErxD5jNU7NDTMg)
+ [Cosmos 的前世今生、未来前景和投资价值](https://mp.weixin.qq.com/s/AmyCTCu6u_3fEgRD2RlxsQ)
+ [Cosmos主网上线了，它将给行业带来哪些改变？](https://mp.weixin.qq.com/s?__biz=MzI5MTQ5NDU3NQ==&mid=2247486805&idx=1&sn=8769bc8f884c89f0bd05e82dd22fd315&chksm=ec0e8295db790b83fbe73bd7ded0dafd0fca93d6b11089bf056e3d05ce667515d46ab48afea3&mpshare=1&scene=1&srcid=&pass_ticket=ZXFSXlAoCmg3o1yqnjc%2Fh8k6L%2Fsjw9vfkYkGOa095ZweYpoUSlvB2Cqdd4UBkp%2FV#rd)
+ https://trycosmos.dev/
* [Cosmos / ATOM Staking Guide](https://bbs.chainon.io/d/3114)
* [区块链的互操作性：Cosmos vs Polkadot](https://bbs.chainon.io/d/3113)

去中心化交易所需求的增加，但如何实现跨链的资产转移和交易，又是一个新的难题。为了解决这个问题，Cosmos 提出了链内通讯协议（IBC），任何人都可以建立一个中心港实现跨链资产的交易，这个过程完全自动化且社区监管。这一功能可以实现去中心化交易所的交易功能，让不同链上的代币也能够自如地转入转出。

![cosmos](/img/cosmos.webp)

Cosmos 的底层是由 Cosmos SDK 构建，Cosmos SDK 可以理解为一个更好帮助开发者开发的工具包，这个工具包含着区块链重要的共识机制，与各种模块化的功能（比如投票功能，治理架构，通信协定），因为 Cosmos SDK 底层构想，使得每个依照 Cosmos SDK 搭建的区块链都具备了相同的基因，可以看到图中都包含了绿色部分的 Tendermint 共识机制及其它上述提到 SDK 模块化的功能，具备相同基因的区块链项目就像留着相同血液的兄弟姐妹只需要通过相似的家族规定（Cosmos 里的 IBC 跨链通信）就可以更容易齐心合作，这就是 Cosmos 构建价值互联网的秘密。

简单来讲，Cosmos 既可以实现商用的区块链快速部署，又可以实现原有区块链系统的信息互通，Cosmos Hub 本体承担起信息中心港的职责。


### PolkaDot vs COSMOS
https://www.8btc.com/article/259379
 -->

### Interledger

## Projects

### Chain

see [https://github.com/chain/chain](https://github.com/chain/chain)

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

## 隐私

### MimbleWimble
官方文档:

+ https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin
+ https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf
+ https://github.com/mimblewimble/grin/blob/master/doc/intro.md


MimbleWimble 白皮书的诞生基于 2013 年发表的一篇匿名论文。这篇论文中使用了单向聚合签名（one-way aggregate signatures ），并搭配一种新的密码原语——配对加密，尽管这种密码原语在学术界不太受信任。此外，MimbleWimble 白皮书还参考了比特币核心开发者 Gregory Maxwell 提出的两项隐私建议——机密交易和 CoinJoin。所谓的 CoinJoin，即当你想要转账时 , 可以找到另外一些也想转账的人 , 你们分别签名自己的输入，共同创建一笔交易。CoinJoin 的核心思想是利用比特币的一笔交易中可以有多个输入以及多个输出这一点 , 将多笔交易合并 , 让人难以分辨哪笔输入对应哪笔输出 , 进而达到难以追踪某个地址的资金的来源或去向的目的。最初的 MimbleWimble 白皮书使用了与比特币相同的椭圆曲线加密技术，引起了诸多比特币研究者的关注。Blockstream 的数学家、应用密码学家 Andrew Poelstra 就是其中一员，他对 MimbleWimble 白皮书进行了改进，于 2016 年 10 月发布了[「precise」版本](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)。长久以来，Andrew Poelstra 的工作重点一直是保护隐私，致力于比特币的机密交易和「无脚本脚本」。

* [Grin 交易原理详解](https://www.longhash.com.cn/news/145)
* [Mimblewimble explained like you’re 12](https://medium.com/beam-mw/mimblewimble-explained-like-youre-12-d779a5bb483d)
* [张韧全面解析 MimbleWimble](https://mp.weixin.qq.com/s?__biz=MzUzNzg4NTAzOA==&mid=2247485610&idx=1&sn=e296a5c4e13cc194b76982af3c145f40&chksm=fae168abcd96e1bdbf7272566de64d69c822d614518a09360f725acc5be444fb3fb5a1035351&mpshare=1&scene=1&srcid=0325fDDI1XeZAhHgF6fCKJjg&pass_ticket=D1SY%2FSFIGgI7aWe8aWZDU7arUhabZE76G0iajOdbaQhRJSkDiBvR36vEcbpjpSnL#rd)
* [Beam and the Mimblewimble Protocol Breathe Life Back into Online Privacy Go to the profile of Alex Broudy](https://medium.com/coinmonks/beam-breathes-life-back-into-online-privacy-38d2c50c807d)
* [Circle 极致解读 : MimbleWimble 及两个实现 Grin 与 Beam](https://www.chainnews.com/articles/179305830457.htm)
* [干货 | Grin 中的交易详解](https://mp.weixin.qq.com/s?__biz=MzIwODA3NDI5MA==&mid=2652527482&idx=1&sn=368fb732aaec0ab82c7c19d95d40347c&chksm=8ce65827bb91d13115692b9d8606b75626ce2f220a8913fcf449661cc733ba04197d199a2f8f&mpshare=1&scene=1&srcid=0331jlqRTwIZqLoaU64CFD8u&pass_ticket=LCwm7pt75YIDrx58YM7d%2BrVl8Z7s6Nuy94z0u4QJdWF%2Fvz7JHcBVvsG%2FQdVXJ7vp#rd)


可以用来改善区块链的隐私，同时通过将无关数据移出区块链来提高可伸缩性。

MimbleWimble允许将多个交易压缩为一个，这样可以节省空间和计算机资源。

MimbleWimble不需要完整的区块链从头开始同步节点。新节点可以在不使用所有历史区块链数据的情况下，以完全安全的方式验证系统的当前状态。

MimbleWimble允许新用户仅使用“内核（kernel）”来同步链，而不是使用历史交易，这将占用大约100字节的空间。比特币有大约4亿笔交易。如果这些都是MimbleWimble交易，那么所使用的总空间将是40GB左右，而不是220GB。因此，MimbleWimble通过这种方式实现了显著的交易压缩。

MimbleWimble 和 confidential transactions 本质上是相同的技术，只是它从区块链中删除了所有非CT特性，从而获得了额外的可伸缩性优势。

工作原理与比特币非常不同，并且有非常严重的限制：

1. 用户必须信任加密技术，以确保自己没有通货膨胀； 
2. 量子计算机可以造成看不见的和无法检测的通货膨胀；
3. 没有能力使用复杂的脚本; 
4. 所有交易都必须使用热密钥与发送方和接收方交互创建。

MimbleWimble并没完全解决“交易隐私”问题，它让交易在区块链上不会暴露隐私，这个实现确实很漂亮，但由于它交易的特殊性（类似 TCP 三次握手，A 给 B 签发交易记录文件，B 响应文件给 A，A 确认上链），这导致相比其他匿名货币（如门罗币、Zcash），基于 MimbleWimble 实现的在链下隐私与安全会遭遇更大挑战。MimbleWimble不会试图模糊交易图。因此，门罗币和ZCash提供了更好的隐私保障。

### 零知识证明

+ https://eprint.iacr.org/2016/046.pdf
    * 入门
+ ZK-SNARK 入门: https://arxiv.org/abs/1906.07221
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-1-introduction-the-medium-of-a-proof-d946e931160
        - https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484148&idx=1&sn=7cc0f42d16cdc9dd3ec9aced08b68ee8&exportkey=AXUOFMzIGB6pC%2Ff5FENqKio%3D&pass_ticket=LkZdJ5gNUYOZr%2F4hH0UU5HjMF%2FmGPzmiyFkfMI6V%2BTtQgKZcUltI5UcJ4OaP6ZZB
    * https://medium.com/@imolfar/why-and-how-zk-snark-works-2-proving-knowledge-of-a-polynomial-f817760e2805
+ https://hashcloak.substack.com/
+ Comparing General Purpose zk-SNARKs
    + https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ http://diyhpl.us/wiki/transcripts/scalingbitcoin/tel-aviv-2019/survey-of-progress-in-zero-knowledge-proofs-towards-trustless-snarks/
+ https://github.com/matter-labs/awesome-zero-knowledge-proofs
+ https://github.com/christianlundkvist/libsnark-tutorial
+ https://www.yuque.com/u428635/scg32w/edmn74
+ https://github.com/sec-bit/learning-zkp
+ https://learnblockchain.cn/2019/04/18/learn-zkSNARK/
+ https://learnblockchain.cn/categories/basic/%E9%9B%B6%E7%9F%A5%E8%AF%86%E8%AF%81%E6%98%8E/
+ https://medium.com/coinmonks/comparing-general-purpose-zk-snarks-51ce124c60bd
+ https://github.com/spring-epfl/zksk
+ https://github.com/AdamISZ/from0k2bp
+ https://zhuanlan.zhihu.com/p/24440530
+ https://www.leiphone.com/news/201803/egFu5MwIsweBU2WY.html
+ https://www.odaily.com/post/5133827
+ https://www.jianshu.com/p/77b44709ca37
+ https://www.chainnode.com/tutorial/4683
+ http://ceur-ws.org/Vol-2344/short11.pdf


> 阿里巴巴知道打开藏着财宝的山洞的咒语。强盗抓住他，让他说出咒语。
如果阿里巴巴说出咒语，就会因为没有利用价值而被杀死。如果阿里巴巴坚持不说，强盗不会相信他真的掌握咒语，也会杀死他。
但阿里巴巴想了一个好办法，他对强盗说：“你们离我一箭之地，用弓箭指着我，你们举起右手我就念咒语打开石门，举起左手我就念咒语关上石门，如果我做不到或逃跑，你们就用弓箭射死我。”

校验一个事件正确与否，并不需要验证者重现整个事件，只要看最终测试结果是否通过即可。

+ 发送的钱属于发送交易的人
+ 发送者发送的金额等于接收者收到金额
+ 发送者的钱确实被销毁了

零知识证明是一种 __基于概率__ 的验证方式，验证的内容包括“事实类陈述”和“关于个人知识的陈述”。验证者基于一定的随机性向证明者提出问题，如果都能给出正确回答，则说明证明者大概率拥有他所声称的“知识”。

Zerocoin（零币协议）将零知识验证用于铸造零币和赎回零币过程中，以隐藏了一笔交易对应的发送方和接收方信息，Zerocash（零钞协议）采用更新颖的zkSNARKs技术，将需要验证的交易内容转换成证明两个多项式乘积相等，结合同态加密等技术在保护隐藏交易金额的同时进行交易验证。缺点在于若网络收到攻击超发零钞，则无法发现或采取措施；Zerocoin和Zerocash均需要进行预先的“信任设置”，没有达到真正的“去信任”。英特尔SGX、zkSTARKs等新技术有可能解决上述问题，但仍需经过实践的检验。

虽然验证者有瞎蒙的可能，但是多轮交互下来，这种可能会越来越小。这样的证明是一种交互的证明方式。双方需要实时交互，交流信息。对于比特币隐私转帐来说，这种证明方式就不太好了。
一个问题是，交互方式是一对一的，发交易的人要向所有矿工证明交易是合法的，一个一个证明效率太低了。

另外一个问题是，既然需要交互，就要求证明过程中双方都在线，这个也会给用户代码很大的不便。最好是有一种非交互式的证明方式，只要证明者给出了证明，后续就不再需要交互，任何人都可以验证这个证明是否正确。但是这明显跟我们一开始说的不能完全由证明者给出矛盾。
一个解决方案就是用公共参考串 ****Common Reference String。
证明者给出的证明里面虽然不像 cut and choose 策略一样，由验证者挑选问题来决定。但是也不是完全由证明者自己来决定，而是根据事先定好的一个种子产生的随机序列决定的。这样就相当于有一个中立的第三方来出题目，同样也能达到效果。当然前提是这个第三方确实是中立的。
就像分粥的例子，一个人先分，但不是另外一个人先挑，而是中立第三方产生一个随机数来决定谁拿哪碗粥。同样可以保证结果尽量公平。

**zcash** 系统也是采用了这样的方案。详细信息可以搜索 zcash trust setup。



#### zk-SNARKS
zk-SNARKS 可以比 confidential transactions 创建更强的隐私保护，但难以扩展，设置也不可信。 

#### Zcash

比特币的替代品，并声称拥有增强的隐私和安全性。

与Monero不同，使用Zcash时不需要 private transactions。 相反，用户可以选择使用增强的隐私功能来混淆交易详细信息，并使用透明的钱包地址或“屏蔽地址”来保持交易的私密性。 为实现这一目标，Zcash利用zk-SNARK，简称“零知识简洁非交互式知识论证”和零知识安全层（ZSL）。

在 Zcoin 里，交易的金额是可以知道的，而采用zkSNARKs技术的Zerocash连交易金额都可以隐密，账本唯一公开记录的唯一内容就是交易的存在性。可以证明对于NP中的所有问题存在zkSNARKs。它引入了多项创新技术，使它们可以在区块链中使用。最重要的是，zkSNARKs减少了证明的大小和验证它们所需的计算量。

#### Zcoin
大家共同维护一个作废列表，存着所有已经花费的零币的序列号。矿工在验证这笔花费交易时运用零知识证明的方法，不需要知道具体花掉哪一个零币，也可以验证零币的序列号是否在作废列表里。由于花费交易并没有输入地址和签名的信息，整个交易过程中，矿工也并不知道这个零币的来源，因此也就难以对交易历史进行分析而获取用户身份。


#### zcoin vs zcash

Zcoin是基于 [Zerocoin论文](http://spar.isi.jhu.edu/~mgreen/ZerocoinOakland.pdf)，而Zcash则是基于 [Zerocash论文](http://zerocash-project.org/media/pdf/zerocash-extended-20140518.pdf)。虽然Zerocoin和Zerocash的论文为相同作者所著，而且它们都使用了零知识证明，但却是完全不同的加密技术，这两个项目之间没有任何关系。

1. Zcash隐瞒每笔交易中发送的金额，而Zcoin则没有。 所以Zcash比Zcoin更不容易受到 privacy timing attacks。 另一方面， Zcash在Zerocash的货币供应中可能未被发现超高通胀 ，这也是Zcash的一个重大权衡。
2. Zcash 参数不可信. Zcoin使用25年前RSA Factoring Challenge产生的参数。 Zcash依赖于假设参数生成中的所有参与者不会串通在一起。
3. Zcash使用 zk-SNARKS 需要的密码学假设还没有经过严格的审查。Zcoin使用1993年推出的RSA累加器作为匿名方案的基础. 除非RSA被破解了，否则无需担心Zcoin。
4. Zcash需要更多的内存使用，发送私有交易所需的时间明显长于Zcoin。 另一方面，Zcoin目前比Zcash需要更多的存储空间。


Zerocash was meant to improve on Zerocoin on these issues:
a) Zerocoin still requires a basecoin to convert back before being allowed to spend. Zerocash has no more basecoin
b) Zerocash's proofs are much more efficient and smaller than Zerocoin's
c) Zerocoin uses fixed denominations to mint (1, 25, 50, 100) while Zerocash is not subject to such limitations
d) Greater privacy with Zerocash since sender/receiver/amount are all obscured.

However Zerocoin's advantage over Zerocash are as follows:
a) While still retaining the basecoin and a lot of the Bitcoin core code, it is a lot easier to integrate to existing Bitcoin merchants/etc.
b) Although Zerocoin's proofs are larger and occupies more storage space, the computational requirements to generate a private transaction are many times faster. Zcash requires large amounts of RAM and minutes of computational time. Zerocoin requires seconds to use and is not memory intensive. Basically Zerocoin uses more storage space but is computationally much less intensive.
c) Parameter generation for both Zcoin and Zcash requires a trusted setup but Zcoin's parameters are arguably less controversial. (https://github.com/zcoinofficial/zcoin/wiki/Parameters-in-set-up-phase-for-Zerocoin-in-ZCoin)
d) Most importantly is that in Zcoin, total supply is still visible so if there's a flaw and someone is secretly creating coins for themselves, this can be much more easily detected. With Zcash, because everything is hidden, if a flaw is exploited, it may be almost impossible to detect!
e) Zerocoin's tech is more peer reviewed and better understood than Zcash's. Zcash's use of zero knowledge proofs uses ZK-Snarks which very few people understand. Even Zooko himself admits he doesn't understand it (https://www.youtube.com/watch?v=P6RLjcGVUnw&feature=youtu.be&t=17m30s). Note that Zerocoin's paper was only like 15 pages. Zcash's paper is more than 50 pages so Zcash's is considerably more complex which means more things that can go wrong. This is why Zcash had to spend so much money on multiple security companies auditing on its critical components and bugs (including some serious ones have been found). A security audit is also not fool proof as the DAO exposed and which is why Zcash also uses multiple companies to audit.



### Schnorr 签名
Schnorr的签名，连同Taproot和无脚本脚本，承诺让所有比特币输出看起来都一样，无论它们属于一个人，还是属于许多人，都代表着托管、Liquid挂钩、闪电通道或智能合约。通过这种方式，他们将大大提高比特币的隐私。

### Monero
Bytecoin 的一个分支

利用秘密地址 （发件人为每笔交易创建的一次性地址）， 环签名 （使用多个签名作为诱饵来混淆发件人地址的方法）和 Ring Confidential Transactions来保护用户隐私, 称为“RingCT”（环签名的改进版本，隐藏了事务中使用的XMR的数量）。

Monero通过独特的拆分机制进一步增加了交易隐私。 每个完整交易被分成不同的金额，并作为单独的较小交易的子集发送，累计达到初始金额。每个金额都有自己的一次性地址。 接下来，在环签名的帮助下，每个单独的交易与各种decoy交易相结合，从而使事务几乎不可能跟踪。

还有 spend keys and view keys. The alphanumeric spend key allows an authorized user to conduct transactions on behalf of the account, while the view key allows permissioned users to look at a specific account’s holdings. This comes in handy when reporting holdings for tax purposes, or auditing a company’s financial reserves.

### Dash
Litecoin 的分支

Dash并非仅以隐私为目的，而是为用户提供交易隐私保护。 

一般的地址和交易都可以在公共Dash区块链上查看。 但是，用户可以利用PrivateSend功能来混淆交易。 Dash 修改 Proof-of-Stake X11  算法, 使用“ CoinJoin ”进行私人交易。

Dash Masternodes（那些始终持有至少1,000个DASH并使用静态IP的人）通过从您的交易中获取 coins 并将其与在网络上发送的多个其他硬币混合来进一步促进隐私和匿名。