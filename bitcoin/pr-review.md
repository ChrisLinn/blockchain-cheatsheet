# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

<!-- 

## #17477 Remove the mempool's NotifyEntryAdded and NotifyEntryRemoved signals (validation)
## #17860 fuzz: BIP 42, BIP 30, CVE-2018-17144 (tests)
## #14053 Add address-based index (attempt 4?) (utxo db and indexes)
## #17639 Add make check-valgrind to run the unit tests under Valgrind (tests)
## #16702 Supplying and using asmap to improve IP bucketing in addrman (p2p)
## #16426 Reverse cs_main, cs_wallet lock order and reduce cs_main locking (wallet)
## #16698 Rework rebroadcast logic to improve privacy (mempool, p2p, wallet)
## #16442 Serve BIP 157 compact filters (p2p)
## #17303 Stop relaying non-mempool txs, improve tx privacy slightly (p2p)
## #15845 Fast rescan with BIP157 block filters (wallet)
## #15921 Tidy up ValidationState interface (validation)
## #15934 Merge settings one place instead of five places (config)
## #16279 Return the AcceptBlock CValidationState directly in ProcessNewBlock (validation)
## #16939 Delay querying DNS seeds if addrman is populated (p2p)
## #16401 Package relay (p2p)
## #16202 Refactor network message deserialization (net processing)
## #15204 Add Open External Wallet action (gui)
## #16688 Add validation interface logging (logging)
## #16512 Shuffle inputs and outputs after joining psbts (rpc)
## #15759 Add 2 outbound blocks-only connections (p2p)
## #15931 Remove GetDepthInMainChain dependency on locked chain interface (wallet)
## #16115 On bitcoind startup, write config args to debug.log (config)
## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)
## #15505 Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)
 -->

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

<!-- 
## #15481
## #15996
## #16060
## #15741
## #15450
## #15834
## #10823
## #15557
 -->

