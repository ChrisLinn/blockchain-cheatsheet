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
 -->

## #16115 On bitcoind startup, write config args to debug.log (config)

https://github.com/bitcoin/bitcoin/pull/16115

+ configuration 通过 config file + 命令行参数
    * https://github.com/bitcoin/bitcoin/pull/15934 解释了 config 是如何被综合起来的
- Bitcoin Core log shrinking
    + ShrinkDebugFile() in logging.cpp
        * by Satoshi: https://github.com/jnewbery/bitcoin/blob/0bbbee96b742e4ad0fd5e1d3c33e4bd2247e4445/util.cpp#L456
    + truncates start of log file if over 10MB * 110% = 11MB
    + called in init at startup, before logging anything else
+ bitcoind does not rotate its own log files, but relies a system's logrotate
+ IBD, Initial Block Download
+ https://github.com/bitcoin/bitcoin/blob/master/doc/reduce-memory.md
+ https://jlopp.github.io/bitcoin-core-config-generator/

## #16345 Add getblockbyheight method / support @height in place of blockhash for getblock etc (rpc)

https://github.com/bitcoin/bitcoin/pull/16345

+ `getblock`  RPC 接受一个 block hash 作为它的第一个参数，然后返回该 block 的相关信息
    * 之前如果想 通过 高度来查的话，需要调用 `getblockhash <height` 然后再调用 `getblock <hash>`
    * 有一些 PR 提出了 改进建议
        * 纯 overloading: PR#8457， PR#14858
            - 可以传 hash 也可以传 高度
                + 但这样 会增加 代码相关处理逻辑的复杂度， 比如 一串hex中都是数字，很难判断这到底是高度还是 hash
                + 就算 overload 了其实参数类型也还是字符串
                    * hex string 或 "@<height>"
            * PR#8457 直接改了 参数 名，会导致不兼容
                + PR#8457 中其实是参考了 getblockstats 中的参数 hash_or_height
                + bitcoin 代码的历史遗留包袱果然很重, getblockstats 中是接受 hash_or_height 其他的一些 RPC 又是接受 hash
                    * getblockheader, preciousblock, invalidateblock, getchaintxstats, getblockfilter, getrawtransaction, gettxoutproof...
            * PR#14858 保留了参数名，但是叫做 blockhash 却可以传 height 会很奇怪 
        * overloading with prefix "@": PR#16317, PR#16439
            - 上面提到的 "@<height>"
            - PR#16439 比较优雅，没什么 duplicate code
        * [新增一个 RPC 接口 `getblockatheight`](https://github.com/bitcoin/bitcoin/pull/16345)
            - duplicate code 太多
        * JSON RPC promise pipelinig
        * [等等](https://github.com/bitcoin/bitcoin/pull/16439#issuecomment-514038924)
+ https://github.com/fanquake/core-review 中有不少 辅助 review PR 的工具
+ 用 height 还是要自己小心处理 re-org 和 orphan 的逻辑，在接近 tip 的时候，by height 不靠谱



## #15505
__Request NOTFOUND transactions immediately from other outbound peers, when possible (p2p)__

https://github.com/bitcoin/bitcoin/pull/15505

+ transaction relay (转发 **未确认** 交易) message flow 的顺序： 
    + `INV -> GETDATA -> TX` 
        * 如果无法给 `GETDATA` 响应一个所求的交易，就会返回一个 `NOTFOUND` 而不是 `TX`
            - 以前会无视 `NOTFOUND`, [PR#15834](https://github.com/bitcoin/bitcoin/pull/15834) 之后引入了对于 `NOTFOUND` 内部维护信息的机制 (clearing the internal map of in-flight transactions). 
            - 这个PR 想要通过马上请求那些给我们发送过该笔交易的 `INV` 的播出节点, 来实际使用 `NOTFOUND` 的信息
    + 这个 PR 触碰了核心的 P2P loop `CConnman::ThreadMessageHandler()` 
        * 给 `SendMessages()` 加了一个序号计数
        * 这个计数器是用来在每次的循环中确定性地选择一个节点来请求交易
            - 这个交易就是另有一人给我们回复 `NOTFOUND` 的那笔交易
+ 什么时候会遇到 `NOTFOUND`
    * 孤儿交易
        - 当节点给我们发送一个我们找不到 其父交易时，我们会向它请求 parent
            + 我们没给他发 INV 就给他直接发了 GETDATA
        - 什么时候会找不到 父交易？
            + 启动节点时
                * 父节点被转发时我们还没启动好
        - 也就是说，重启时 收到 INV 有可能 不按顺序：在收到父交易之前就收到了子交易
+ blocks-only clients
    * blocks-only clients 会用 [VERSION message](https://btcinformation.org/en/developer-reference#version) 中的 relay flag 来说明他们不转发交易
+ 区块重组 (re-org) 逻辑
    * 1) get new block and see that it's got more work than tip
    * 2) run ActivateBestChain, which tries to move to the best chain, which will 
        - 2a) rewind blocks, adding txs to mempool
        - 2b) connect new blocks, removing txs from mempool that in the new block
- 为什么这个PR中按顺序挑选拨出节点，而不是随机
    + 就怕找之前要过的节点们又要一次啊
+ 平均连接多少节点？
    * 8 outbound + 117 inbound = 125
+ 比起 inbound (主动连接我们的)，我们更加信任outbound（我们主动连接的）peer
    * 怕被 inbound peers 日蚀攻击，withhold 交易
+ an attacker could circumvent that by sending us NOTFOUND right before the GETDATA times out, and then we would be likely to ask the tx from another inbound node, that is also controlled by the attacker.
    * time delay attack
        - https://github.com/bitcoin/bitcoin/pull/14897 想要修复这个问题，但是又引入了新bug
            + retry logic 很容易出错，很多 edge cases
+ bitcoin 中 P2P 功能测试框架 很难测试 PR
    * 受限于 _CConnman_ 和 _MockNetworkRouter_
    * 目前很难完全模拟 inbound/outbound behavior  
    * 可以看看 https://github.com/bitcoin/bitcoin/issues/14210 中的讨论
    * 比如 相同子网或 IP 下的 peers 就需要特殊逻辑
        * 有办法解决，比如用 namespace 来 fake IP


## #15713
__refactor: Replace chain relayTransactions/submitMemoryPool by higher method (wallet)__

https://github.com/bitcoin/bitcoin/pull/15713

+ 以前的 代码，wallet 和 node 的 功能杂糅在一起
    + 比如这个 pr 中，由于历史原因, wallet 仍可以调动 node 广播交易给peer
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

检查 policy 之前会先检查 consensus rules

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

