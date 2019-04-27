# Ethereunm

## evm

https://learnblockchain.cn/2019/04/09/easy-evm/


## Gas

## ERC20, ERC721

## The DAO

## TPS 扩展

### 类别
+ State Channels(状态通道)类别
    * __uRaiden__
    * __Liquidity Network__
    * _Decentraland_
    * Counterfactual
    * FunFair
+ Sidechains(侧链)类别
    * Plasma
    * Parity Tech
    * POA Network
+ Sharding(分片)类别
    * Diamond Drops
+ Multi-chain(跨链)类别
    * Cosmos
+ Alternative(其他)类别
    * TrueBit


### Casper：POS共识机制

layer 1

### Sharding

layer 1 链上（on-chain）扩容(需要更改区块链底层协议)

将区块链划分成不同的部分（shards），每个部分独立处理交易。


### Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

### Plasma 子母链(Side Chain)

链下（off-chain）扩容

Plasma是一系列在根区块链（root blockchain）（即以太坊区块链）上运行的合约。根网络合约只处理少量来自子区块链的请求，在大多数情况下，子区块链能够完成大量的计算。来自子区块链的请求定期在根区块链中广播。可以把根区块链看做最高法院，所有下级法院均从最高法院获得权力。

将区块链组成一个树层次结构，并将每个区块链作为单独的分支区块链处理，每个分支都带着 增强版区块历史记录和 被提交到 merkle proof 中的 MapReducable 计算。

two key parts:

+ Reframing all blockchain computation into a set of MapReduce functions
+ an optional method to do Proof-of-Stake token bonding on top of existing blockchains with the understanding that the Nakamoto Consensus incentives discourage __block withholding__
