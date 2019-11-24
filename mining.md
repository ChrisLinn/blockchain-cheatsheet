# Mining

+ [Realtime mining hardware profitability](https://www.asicminervalue.com/)
+ [WhatToMine](https://whattomine.com/)

## BetterHash
可能会 替代 stratum, 可以用来解决 矿池不需矿工同意挪动算力改挖其他币

## 挖矿进化史
历史上,各种 PoW 数字货币曾设计了若干种
Anti-ASIC 算法,试图达到这一目标,但这些算法都不尽人意。这些算法大致可
以分为两类:

+ 第一类是试图增加完成 PoW 所需要的局部存储,例如 LiteCoin 需要 128KB 的存储,而 Monero 需要 2MB 的存储。但在 28nm 以及更先进的工艺下,在集成电路片上集成 2MB 的 SRAM 已经不再困难。LiteCoin 的 ASIC 矿机早已诞生。而 Monero 的矿机最近也被生产出来了
+ 第二类是试图增加完成 PoW 所需要的 DRAM 带宽和 DRAM 空间,典型的代表是以太坊和 ZCash。这两种数字货币目前的矿机以显卡矿机为主。但值得注意的是,比特大陆已经量产了以太坊矿机 E3。可以预见,ASIC 矿机逐步替代显卡矿机将是不可避免的趋势。原因在于带宽密集的挖矿算法仅仅使用了显卡的 DRAM 访存带宽,对于显卡上的计算资源几乎没有使用,因此,可以设计类似显卡但计算资源大幅缩减的芯片,它的功耗和成本极低,但仍可执行带宽密集型的 PoW 算法。

## Scrypt 算法
+ [Scrypt 算法](altcoin/scrypt.html#scrypt-%E7%AE%97%E6%B3%95)

## PoS
+ https://www.stakingrewards.com/
+ https://github.com/wetez-project/pos-book
+ [币龄](blockchain/readme.html#pos-proof-of-stake%E6%9D%83%E7%9B%8A%E8%AF%81%E6%98%8E)
    * PoS 矿池, 超级节点

## Mining pool
+ difficulty
    * `difficulty = difficulty_1_target / current_target`
        - target is a 256 bit number
        - `difficulty_1_target` can be different for various ways to measure difficulty. Traditionally, it represents a hash where the leading 32 bits are zero and the rest are one (this is known as "`pool difficulty`" or "`pdiff`"). The Bitcoin protocol represents targets as a custom floating point type with limited precision; as a result, Bitcoin clients often _approximate_ difficulty based on this (this is known as "bdiff").
            + bdiff vs pdiff
                * 公式算出值 vs 矿池使用的 non-truncated 值
                * 比如
                    - bdifficulty_1_target = 0x00000000FFFF0000000000000000000000000000000000000000000000000000
                    - pdifficulty_1_target = 0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
    * Each block stores a _packed_ representation (called "Bits") for its actual hexadecimal target. The target can be derived from it via a _predefined formula_.
    * maximum difficulty
        - maximum_target / 1
    * minimum difficulty
        - maximum_target / maximum_target = 1
    * hash_rate
        - hash target for diff_1:  `0xffff*2^208`
        - hash target for diff_D: `0xffff*2^208 / D`
        - expected number of hashes we need to calculate to find a block with difficulty D: `D * 2^256 / (0xffff * 2^208)` = `D * 2^48 / 0xffff`
            + 即概率的倒数
        - one block found every 10 minutes (600s)
            + hash_rate = `D * 2^48 / 0xffff / 600` = `D * 2^32 / 600`
    * time to find a block
        - `difficulty * 2^32 / hashrate`
+ [miningpoolstats](https://miningpoolstats.stream/)
+ extra nonce
+ stratumn protocol
    * https://github.com/sammy007/open-ethereum-pool
    * https://github.com/sammy007/monero-stratum
    * https://github.com/luke-jr/eloipool
+ solo pook
    * http://solo.ckpool.org/
+ keep alive
+ bits, int, difficulty, hashrate
    * 用前导零来估算 int
    * (pool miner) difficulty
        - 收益计算
    * retarget
+ 什么是拒绝数？拒绝率？
    * 根据矿池的工作原理，矿池不断给矿机下发新的计算任务，矿机将计算结果提交给矿池。但是由于矿机与矿池的网络连接是有延迟的，从矿池下发新的挖矿高度的任务给矿机，到矿机接收到任务，在这段时间提交的结果已经过时，将不被矿池承认。拒绝率即无效提交与总提交结果之比。
    * 拒绝数是指矿机提交的算力不符合服务器要求，被服务器拒绝的工作量。拒绝率是指拒绝数占总提交数的比例，数值越小，矿机的工作效率就越高。
+ 幸运值
    * 每个区块的幸运值是矿池实际工作量与挖矿难度之比。如果工作量小于难度，说明运气比较好，幸运值就越高。挖矿的运气成分很大，幸运值发生波动也很常见，但挖矿时间越久，总幸运值越接近 100%。
+ 结算方式
    * PPS
        - 收益稳定，只要矿机正常工作就有收益，收益和提交的工作量有关，和矿池幸运值、交易手续费无关
    * PPS+
        - 该结算方式蚂蚁矿池将交易手续费分配给矿工，所以交易手续费高，收益高；交易手续费低，收益低
    * PPLNS
        - 收益与矿池幸运值有关，矿池幸运值高，收益高；矿池幸运值低，收益低
    * SOLO
        - 矿机爆块才有收益，如果矿机爆块可获得该块扣除挖矿手续费的所有收益，如果矿机不爆块则没有收益

## Agent, Proxy
挖矿的代理早期是为了兼容协议，现在还用这个的一般是大矿场，可以节约带宽提高效率，特别是下发任务的时候，如果一千台机器，原来要同时接收1000次 ，用代理之后与矿池之间只有一次。内部再广播。

## 矿池优化指南
* avoid orphan block
    - __Information Propagation in the Bitcoin Network__ 中提出了几个优化传播的建议
        + Pipelining block propagation
        + Minimize verification
        + Connectivity increase
            * latency
                - important otherwise too late to start mining
                - num of conn isnt the key, unless there are only a few
    - 并指出了 _Pipelining block propagation_ 和 _Minimize verification_ 只能达到不显著的优化效果，重点还是 _Connectivity increase_
        + Pipelining block propagation
            * 就是及时告诉别人有新块, 叫别人来拿
                - 针对矿池
                    + pool 每秒轮询wallet
                    + wallet 主动udp通知 pool
                        * wallet 生成新块的时机，除了 pool submit to wallet 之外，对wallet收到同步的块也主动生成
        + Minimize verification
            * 先 验过 difficulty，尚未验 merkle 就把新块告诉别人（因为要伪造能过 difficulty check 的成本也很高，所以不必担心这方面的攻击）
        + Connectivity increase
            - __Information Propagation in the Bitcoin Network__ 采用了星型连接
            * 矿池与用户的带宽足够
            * 钱包上行速度足够
            * 多钱包
            * 高速传播网络? 如果区块能在高速传播，孤块率会降低了?
                - [BTC 的 fibre 网络](http://bitcoinfibre.org/)
                - eth 上全网到处搞节点, 最终的效果还不如不连节点, 就让自己发现
                    + 但如果是 连接较差(比如海外)IP / 个人IP, 孤块概率就会较大
    - 算力大的会好些, 算力大了连爆几个块，自己的不会成为孤块
        + 刚开的时候没算力肯定纠结
    - ETH 叔块率高. 运气好 ~8%。运气不好 10~20%
        + 出块时间快，网络传输延迟(国外也有不少算力，国外传播过来的延迟影响比较显著)导致
        + 为了降低叔块就要自己建立网络
        + 像 btc 这类，可以使用 get-block-template 挖出后向自己在全球部署的多个节点同时 submit-block
            * 而不是 打包过后的 header hash，即work，但 eth 和 btm 目前都只有 get-work 。
        + 鱼池的做法是，如果现在获取的是 a 钱包的任务对应的 get-work，就把这个任务的数据同步到其他钱包，然后同时 submit
            * 比起自己的节点之间保持直连，挖出后才用区块同步机制进行同步，效果更好
    - 叔块率难以降到 10以下
        + 除非都挖空块？
            * 空块也有区别
                - coinbase tx
        + 挖空块没意思
            * eth 交易费很高，不挖交易很吃亏
            * 现在都是 pps+，交易费高时也提高了收益
                - 如果挖空块，就算没有叔块矿池也要亏钱
* DDoS
    - 一层硬件防火墙
    - 软件防火墙不太行
* [藏块攻击](/blockchain/attack.md)
