# Attack

__目录：__
- [Double-spending Attack](#double-spending-attack)
- [51% Attack](#51-attack)
- [Gas Limit Attack](#gas-limit-attack)
- [DoS with Block Gas Limit](#dos-with-block-gas-limit)
- [N@S Attack](#ns-nothing-at-stake-attack)
- [Long-Range Attack](l#long-range-attack)
- [未来区块时间攻击]()
- [BGP Attack](#bgp-attack)
    - [Partition Attack](#partition-attack)
    - [Delayed Attack](#delayed-attack)


## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

## 51% Attack

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块

## BGP Attack
我觉得，比起double-spending 和 51%，其实更容易被黑客组织实施——通过攻击 ISP 网络服务提供商进行 BGP（边界网关协议，一种简单时代产物）攻击。考虑到，30%的比特币网络存在在13个主机中，60%的比特币流量在3个ISP中可见，BGP攻击的威胁性很大。 

[研究者揭露比特币区块链遭遇安全漏洞威胁](https://bitcointalk.org/index.php?topic=1873692.0)

### Partition Attack
Partition Attack 分区攻击阻止比特网络的两/多部分连接起来，一般来说这样的ISP是连接这些比特连接网络的唯一路径。当然被分割的区块还会分别继续拓展，但是当重新连接的时候，必然有一边需要被丢弃——如果你这边展的慢，那么开采的币、交易、采矿收入就不得不被丢弃。
### Delayed Attack
带来更大问题的可能是 Delayed Attack 延迟攻击。延迟攻击没有好办法检测，商户们在遭遇到这种攻击后，可以重复消费（交易没被确认没被广播出去）。矿工们的交易处理能力也将被浪费，普通的节点无法广播区块链的最新版本。

