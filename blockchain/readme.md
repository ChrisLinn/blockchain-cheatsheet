# Blockchain

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




+ merkle tree root hash 作用
+ 如果给定一个 tx hash，最少还需要知道哪些信息才能确定该 tx 是否在一个block中
+ 


## Double-spending Attack
区块链不能完全防止 double-spending attack 双花攻击。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

## 51% Attack


## 公有链 vs 联盟链 vs 私有链


## Consensus 共识
针对非拜占庭错误的情况，一般包括 Paxos、Raft 及其变种。其中Raft比起Paxos容易理解，容易实现。

对于要能容忍拜占庭错误的情况，一般包括 PBFT 系列、PoW 系列算法等。
从概率角度，PBFT 系列算法是确定的，一旦达成共识就不可逆转；而 PoW 系列算法则是不确定的，随着时间推移，被推翻的概率越来越小，同时使用经济上的惩罚来制约破坏者。如PoW需要付出超过系统一半的算力的经济代价。

### PoW, Proof of Work, 工作量证明
__穷举 nounce__，参与计算 hash，将结果值与当前网络的目标值 target
 做对比，如果小于目标值，则解题成功，工作量证明完成。

target 目标值 = 最大目标值（恒定值） / 难度值

难度值动态调整，每两周（2016个区块）调整一次，使得平均每10分钟出一个新块

将导致 __算力集中__，计算资源大的有优势


### PoS, Proof of Stake,权益证明
为了使每个 Block 更快被生成，PoS 机制 __去掉了穷举 nonce__，一个账户的 __余额__ 越多，在同等算力下，就越容易发现下一个区块。将导致 __大户集中__ 。

在PoS对PoW进行改进后，又引进了新的问题 N@S (Nothing at stake) Attack 和 Long-Range Attack。
#### N@S (Nothing at stake) Attack
#### Long-Range Attack



### DPoS, Delegated Proof of Stake, 委托股权证明
放弃了完全的去中心化，不定时的选中一小群节点，这一小群节点做新区块的创建，验证，签名和相互监督。有点像 __人民代表大会投票制度__ 。这是一种去中心化和中心化的权衡，Peer 和 Peer 地位不完全平等。将导致 __见证人集中__ 。



### PBFT 实用拜占庭容错算法
也是通过投票来达成共识，可以很好的解决包括分叉等问题的同时提升效率。但仅仅比较适合于联盟链私有链，因为两两节点之间通信量是O(n^2)（通过优化可以减少通信量），一般来说不能应用于超过100个节点。

存在的问题是  可扩展性（scalability）差



## [Chain](https://github.com/chain/chain)
企业级的区块链平台架构，针对金融领域，金融机构可以在上面创建和发行数字资产。共识协议采用联邦拜占庭协议，支持多种数字资产，适合联盟链。

## OpenLedger
去中心化交易平台。用户可以拥有自己的私钥，交易期间用户的资金仍在自己的手上，交易所的资金全部都在区块链上，完全透明，同时用户可以保持他们的隐私，而交易所的资金则可以被任何人在任何时候进行审计。

交易速度可高达10万次/秒，纳斯达克级别。

为什么要搞 open ledger？大型交易所失窃案件揭示了去中心化交易所的重要性。

## Tendermint

## Polkadot

## Cardano

## BitShares

## 隔离见证


## BGP攻击
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 [研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)
### partition attack 分区攻击
阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### delayed attack 延迟攻击
带来更大问题的可能是延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

### 主网

### 资产上链
