# Attack
需要考虑的安全问题:

+ 直接获益
+ 作废一条链
+ 低代价消耗资源
+ 攻击网络节点
    * 拒绝服务
    * 网络隔离
    * 欺骗节点

## Double-spending Attack
区块链不能完全防止 double-spending attack（双重花费攻击，双花攻击）。

没有 confirmations 的是无法防止双花的。

但是采用多次confirmations可以降低其发生的概率。如果攻击者拥有的算力不到25%，那么发生双花攻击的概率将降到千分之一 ( (25%/75%)^6 , 倘若掌握了全网 25% 的算力)。

比特币运用了经济学原理，来防止双花发生，攻击者的第二次交易要被更快确认，要控制强大的算力来追赶分块增长，成为最长链。从经济的角度考虑，拥有如此强大算力的人做这个事是不划算的，这个过程中浪费的资源用来正经挖矿得到的收益更高。

### 51% Attack (Majority attack)
The attacker controls more than half of the network hashrate.

No amount of confirmations can prevent this attack; however, waiting for confirmations does increase the aggregate resource cost of performing the attack, which could potentially make it unprofitable or delay it long enough for the circumstances to change or slower-acting synchronization methods to kick in. 

### Finney attack 芬妮攻击
芬妮攻击，是由Hal Finney 命名的，他也是描扣块攻击的第一人。这种攻击是一种双花攻击的变化，与0确认交易相关。

攻击者生成一个有效的块但是不会广播这个块，但是会广播交易A，交易A是指购买一个物件或者服务。商家会看到没有任何冲突的交易A并接受0确认交易。之后攻击者会广播已生成的有效块和与交易A有冲突的交易B，这时比特币网络会接受有效块并使交易A无效。

攻击的代价是非常大的，因为在攻击者生成块和完成交易A之间存在时间间隙，在此期间网络上的其他人也可以生成有效块并广播它，从而使攻击者生成的有效块变的无效。因此，只有在成功购买到商品后并且立即释放扣押的区块时，这个攻击才是有效的。

这种攻击的一般形式，Satoshi Nakamoto 在《白皮书》的第11章有提到。理论上，攻击者可以预先生成任一数量的区块，例如，商家在释放产品前需要一次确认，攻击者将在网络前预先生成两个块，并且使用双倍手续费去广播它。但是每个区块的成本呈指数上升，而且在发布购买前需要接受6次确认的验证使这种攻击只有在攻击者接近网络哈希率50%或者更高的情况下有可能发生。

### Race attack 竞争攻击

和 芬妮攻击 的区别是，芬妮攻击是 0确认交易 vs 冲突块，竞争攻击是 0确认交易 vs 冲突交易.

The attacker supplies an unconfirmed transaction to the victim that pays the victim. Meanwhile, they broadcast a conflicting transaction to the network. As the merchant saw their own transaction first, they are under the illusion of getting paid, while the rest of the network predominantly saw the doublespend first and thus it's likely the merchant will in fact not get paid.

It requires the recipient to accept unconfirmed transactions as payment.

This attack is much easier to pull off, when the attacker has a direct connection to the victim's node, and perhaps deposits the conflicting transaction directly to miners.

It is therefore recommended to turn off incoming connections to nodes used to receive payments, so that your node will seek their own peers, and not to allow the payer to directly submit the payment to the payee.

precautions (to lessen the risk of a race attack but the risk cannot be eliminated):

+ disable incoming connections
+ choose specific outgoing connections, only connect to well connected nodes

The Sybil attack takes this information asymmetry even further as the attacker isolates the victim from the network and restricts their access to information while doublespending them. Other doublespend attacks rely on having premined a transaction to yourself in a block kept secret before paying a victim with a conflicting transaction (Finney attack), or on having overwhelming hashrate (majority attack).


### Vector76 attack Vector76攻击
也被称为一次确认攻击，是 race attack 和 fenny attack 的组合，使得甚至具有一次确认的交易仍然可以被逆转。 对于 race attack（没有传入连接，与连接良好的节点的显式传出连接）相同的保护方式显着降低了发生这种情况的风险。

It is worth noting that a successful attack costs the attacker one block - they need to 'sacrifice' a block by not broadcasting it, and instead relaying it only to the attacked node.

参阅:

+ http://bitcointalk.org/index.php?topic=36788.msg463391#msg463391
+ http://www.reddit.com/r/Bitcoin/comments/2e7bfa/vector76_double_spend_attack/cjwya6x

### Alternative history attack 替代历史攻击

可以理解为 需要多次确认的场景下的芬妮攻击

If the attacker controls more than half of the network hashrate, the Alternative history attack has a probability of 100% to succeed. 

This attack has a chance to work even if the merchant waits for some confirmations, but requires relatively high hashrate and risk of significant expense in wasted electricity to the attacking miner.

The attacker submits to the merchant/network a transaction which pays the merchant, while privately mining an alternative blockchain fork in which a fraudulent double-spending transaction is included instead. After waiting for n confirmations, the merchant sends the product. If the attacker happened to find more than n blocks at this point, he releases his fork and regains his coins; __otherwise__, he can try to continue extending his fork with the hope of being able to catch up with the network. If he never manages to do this then the attack fails, the attacker has wasted a significant amount of electricity and the payment to the merchant will go through.

The probability of success is a function of the attacker's hashrate (as a proportion of the total network hashrate) and the number of confirmations the merchant waits for. 

For example, if the attacker controls 10% of the network hashrate but the merchant waits for 6 confirmations, the success probability is on the order of 0.1%[3]. Because of the opportunity cost of this attack, it is only game-theory possible if the bitcoin amount traded is comparable to the block reward (but note that an attacking miner can attempt a brute force attack against several counterparties at once).

## Gas Limit Attack
以太坊中虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

## DoS with Block Gas Limit
上面提到以太坊中需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。


## N@S (Nothing at stake) Attack

## Long-Range Attack

## Sybil Attack 女巫攻击

攻击者利用单个节点来伪造多个身份存在于P2P网络中，从而达到削弱网络的冗余性，降低网络健壮性，监视或干扰网络正常活动等目的。

在这情况下，他们可以拒绝接收或传输区块，甚至进行覆盖51％的系统攻击。

## Eclipse Attack 日蚀攻击

提出此攻击的论文《Majority is not Enough: Bitcoin Mining is Vulnerable》

+ 可以将比特币系统的安全阈值从50%降低到33%
    * 可以将整个比特币网络划分为两个部分，攻虽然在全网只占有40%的算力，但在左/右两侧的子网络中却分别拥有50%以上的算力，从而实现51%攻击。
+ 即使攻击者没有包含任何算力，其也可以通过日蚀实现双重花费攻击
+ 以太坊是否存在日蚀攻击？
    * Wüst K, Gervais A. Ethereum eclipse attacks[R]. ETH Zurich, 2016.（以太坊中的日蚀攻击）
    * Marcus Y, Heilman E, Goldberg S. Low-Resource Eclipse Attacks on Ethereum's Peer-to-Peer Network[J]. IACR Cryptology ePrint Archive, 2018, 2018: 236.（[以太坊点对点网络中的低资源日蚀攻击](http://www.cs.bu.edu/~goldbe/projects/eclipseEth.pdf)）
        - Goldberg描述说，在以太坊网上发起一次日蚀攻击，与在比特币上发起一次攻击是“完全不同的”。为了实现对比特币的一次日蚀攻击，一个攻击者需要控制大量IP地址(机器)来垄断连接到受害者节点的连接，这使得攻击比特币的代价极高。与此相反，仅使用一两台机器就能在以太坊层发动类似的攻击
        - 主要是因为比特币依赖于一个非结构化网络节点的随机相互连接，而以太坊则依赖于结构化的基于一个称为 __Kademlia__ 的协议的网络，其旨在更有效地允许一个节点可以连接到其它节点。以太坊的点对点网络中的节点由其公钥所标识。显然，以太坊的版本(在Geth v1.8.1之前)允许用户运行无限数量的节点，每个节点都有一个不同的公钥，从同一个IP地址的同一台机器上运行。通过使用密钥生成算法，攻击者可以非常快地创建 __无限数量__ 的节点ID(在对等网络上的标识符)。更糟糕的是，攻击者甚至可以 __DIY__ 节点ID，使其相比于随机的节点ID来说对受害者更具吸引力，这样就能基本上把受害者 __拉到__ 他们身边 。

## 重放攻击
+ 2016 年 7 月以太坊进行硬分叉的过程中发生
+ 每条链上的地址和私钥生产算法相同，交易格式也完全相同
    * 在其中一条链上的交易在另一条链上很可能是完全合法的
    * 使用者在其中一条链上发起的交易，就可以到另一条链上去重新广播，也可能得到确认。
        - 以太坊分叉时几乎所有交易所也都没意识到这个问题，更没有提前做ETH和ETC分离， 这时候只要有人从交易所提取ETH币，就有可能得到同等数量的ETC币。许多人利用这个漏洞，不断在交易所充币和提币（ETH）， 从而获取额外的ETC。
        - 或者 发出一笔 a 链交易，结果被人拿到 b 链上去重放了，那么 b 链上的资产也损失了。
    * BCH 分叉时在交易数据签名添加了 SIGHASH_FORKID(0x40)，使交易数据在两条链上不再互相兼容，

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

## Tor

[Bitcoin over Tor isn’t a good idea](https://arxiv.org/pdf/1410.6079.pdf):

+ 中间人攻击
    * control which Bitcoin blocks and transactions are relayed to the user and can delay or discard user’s transactions and blocks
+ an attacker can fingerprint users and then recognize them and learn their IP address when they decide to connect to the Bitcoin network directly.

## Block Withholding Attack 扣块攻击

自己计算出正确的  hash  后，没有回传给矿池, 可能的目的:

+ 降低矿池的收益
+ 私自广播出去, 区块奖励自己独吞
    * 要 blocktemplate 才能实现？


### 芬妮攻击
扣块攻击最简单的形式也叫作芬妮攻击

### Selfish-Mining Attack 自私挖矿攻击

攻击者挖到新区块后藏起来不公布，其他诚实矿工因为不知道新区块的存在，还是继续在旧区块基础上挖矿。等到攻击者挖到第二枚区块后便会同时公布手中藏着的两枚区块，这时，区块链分叉就出现了。只要攻击者比诚实矿工多挖一枚区块，攻击者所在的分叉就是最长链：根据比特币的共识机制，矿工只在最长链后面挖矿。因此，原本诚实矿工们所在的那条链，因为比攻击者的分叉短，便作废了。此时此刻，攻击者因为挖到了两枚新区块而获得相应收益；而诚实矿工的分叉被废弃，他们什么也得不到。

Block Withholding Attack 扣块攻击的一种, 进行自私挖矿的攻击者只需要拥有全网 25%? 33%? 的算力，就可以保证自己获得更多的收益了。[如果你能在竞争中和别人五五开，你只要有25%的算力，自私挖矿就是更有利的选择。就算你在竞争中总是会输，如果你有33%的算力，自私挖矿也是严格有利的。](https://www.zhihu.com/question/21976182)

解决办法: 新的难度调整公式, 用于更新挖矿难度的参数，应该是衡量网络的实际算力。一种想法是在难度调整公式中加入孤块数量的因素。这可通过矿工们来实现，即指示他们挖到的区块中存在“uncle”（通过包含它们的区块头，及对等节点中继这些数据. 或者一个简单的解决方案, 矿工收到两个竞争区块的时候, 随机抽取其中一个进行挖矿.

许多针对“自私挖矿”策略优化及扩展的工作相继展开。2016年，Nayak等作者在文献(K. Nayak, S. Kumar, A. Miller, and E. Shi, “Stubborn mining:Generalizing selfish mining and combining with an eclipse attack,”in 2016 IEEE European Symposium on Security and Privacy (EuroSP), Saarbr ¨ucken, Germany, Mar. 2016, pp. 305–320.)中提出了一种新的挖矿策略“stubborn”，该策略对“自私挖矿”策略进行了扩展。基于该策略，恶意矿池的收益相较于使用“自私挖矿”策略将提高13.94%。不仅如此，在文中作者还进一步对“stubborn”策略进行了优化，并提出了两个新的策略，即“the EqualFork Stubborn”与“Trail Stubborn”。这两个策略进一步提高了恶意矿池的挖矿收益。