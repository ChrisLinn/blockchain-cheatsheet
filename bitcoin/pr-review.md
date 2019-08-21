# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

## #15505
## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
- 交易什么时候会被广播呢？
    + 新块 (a new block, a new chain tip)到来时，如果交易池中的交易没被包含在新块，则会被广播
        * 主要是为了防止当发送交易时，网络掉线没能广播出去。
            - 本来最好在wallet层通知一下user，该tx没被relay成功。但这只是一个重构的PR，所以保持原有逻辑。
        * 注意会造成 privacy 问题，后续会改进
            - 目前的想法是 rebroadcast 的交易里面，不仅有你的，也有别人的交易。避免被知道哪个交易是哪个节点发出来的。
- submit tx 到 mempool 失败可能的原因？
    + https://github.com/bitcoin/bitcoin/pull/15713#discussion_r306122680
        * consensus-invalid or policy-invalid tx
            - duplicate tx
            - non-standard tx
                + https://bitcoin.stackexchange.com/questions/52528/how-is-a-standard-bitcoin-transaction-defined
            - exceed MAX_FEE_EXCEEDED
            - below minRelayTxFee
            - ...
        * 交易池 overflow
        * ...
- 什么是 locking order？ 为什么要按同样的顺序 acquire lock？
    + thread consistency, 防止可能的 死锁
    - 比特币编译时 可以开启 `--enable-debug` 检查 lock 顺序

## #15169 
__Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)__

https://github.com/bitcoin/bitcoin/pull/15169

将交易放进mempool的 checkinput 由串行改造成 并行

check against policy before consensus rules

policy 是入池原则，consensus是验块原则, __policy 会更严__

check input 的 validity
一般会检查:

+ double spend
+ script & sigs
+ amount

改成并行之后 只检测 
script & sigs

但也是有道理的，
verify valid inputs
而不是 verify valid tx

## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557


<!-- 
OPTECH

---
Brainstorming just-in-time routing and free channel rebalancing: sometimes LN nodes receive a routed payment that they reject because their outbound channel for that payment doesn’t currently have a high enough balance to support it. Rene Pickhardt previously proposed Just-In-Time (JIT) routing where the node would attempt to move funds into that channel from one or more of its other channel balances. If successful, the payment could then be routed; otherwise, it would be rejected like normal. Because the routed payment might fail for other reasons and prevent the routing node from earning any fees, any JIT rebalance operations need to be free or they could end up costing the node money in a way that attackers could exploit.

In a new post to the Lightning-Dev mailing list, pseudonymous LN developer ZmnSCPxj describes two situations in which other profit-maximizing nodes might allow free rebalances. The first case is the observation that the next hop in the route will receive its own routing fee paid by the spender if the payment succeeds. ZmnSCPxj describes a method by which the next hop’s node can make their part of the rebalance contingent on receipt of the routing income, ensuring that they either get paid or the rebalance doesn’t happen. This would require additional communication between nodes and so it’s a change that probably needs further discussion in order to be considered for addition to the LN specification.

The second case ZmnSCPxj describes is other nodes along the rebalance path who themselves want to rebalance one or more of their channels in the same direction as the routing node. These nodes can allow free routing in that direction to encourage someone to perform that rebalancing. This second case doesn’t require any changes to the LN specification: nodes can already set their routing fees to zero, allowing any other nodes to attempt JIT routing with free rebalances. The worst case would be that a payment that would’ve failed anyway will take a bit longer to return a failure message to the spender, a delay equal to the amount of time any routing nodes spent attempting to rebalance their channels in order to support the payment.
---

 -->