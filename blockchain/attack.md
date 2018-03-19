# Attack

## Double-spending Attack，双重花费攻击，双花攻击
区块链不能完全防止 double-spending attack 双花攻击。

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

# 未来区块时间攻击
* 攻击者意图通过篡改未来时间戳来降低挖矿难度，从而控制主网进行私自挖矿最后牟取大量coin收益。
    - 元界Metaverse硬分叉事件
        + 没有深刻理解共识时间的参数, 用了libbitcoin的库 比特币10分钟情况下的 timestamp偏移, 而他自己本身是10s左右一个块