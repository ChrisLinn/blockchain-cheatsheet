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

在PoS对PoW进行改进后，又引进了新的问题 [N@S (Nothing at stake) Attack 和 Long-Range Attack](./attack.md)。



### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差

### Tendermint

+ Tendermint共识引擎通过Tendermint套接字协议（TMSP）与应用程序进行连接。

### Ouroboros 乌洛波罗斯
+ provable PoS
+ 已有的 PoS 有着诸多缺陷，且无法证明其安全性。为了确保区块链的安全性，选择股权者来产生区块的方法必须是 __真随机__ 的。乌洛波洛斯是第一个安全性经过形式化证明的权益证明共识。

## 主网

## 侧链

## 跨链

## 链池

## 多链技术

### Polkadot

### COSMOS

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
