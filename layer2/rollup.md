## 错误性证明（Fraud Proof） vs 有效性证明（Validity Proof）
+ 错误性证明即表示某个状态转换不正确的证据。这种方案反映了一种乐观的态度：假设 区块上表示的 Layer-2 状态都是正确的，除非有人能证明不是。实际上，提交到链上的区块也很有可能包含着一次不合逻辑的状态转换。
    * 无需为每一次状态转换都提供证明，只在系统需要中断的时候提供。因此，错误性证明方案需要的计算资源更少、更适合可扩展性受限的环境
    * 协议会将沉默（即挑战者的缺席）视为默示的同意。实际上，攻击者完全可以尝试用 DDoS 攻击制造出表面的沉默。
    * 纠纷时间窗口（DTF）
        - DTF 时间越长，发现错误状态转换的几率就越高。但同时，时间越长，用户需要等待的时间也越长（比如需要取款的时候）。
+ 有效性证明即表示某个状态转换正确的证据。这种方案的态度更为消极：当且仅当某个状态是正确的，区块才应该包含代表相应 Layer-2 状态的值。
    * 向一个智能合约发送一些链下计算已然发生的证据。智能合约仅在一个新值被证明为正确之后才更新区块链。有效性证明的主要优点是区块链上总是能反映出一个正确的 Layer-2 状态，而且一个新状态可以即时使用。而主要缺点就是每个、每次状态转换都需要一个证明，不单单是状态转换受到质疑时才需要提交证明，这就影响到了其可扩展性。
    * 昂贵而且繁琐
+ ![](https://upyun-assets.ethfans.org/uploads/photo/image/2db60a6dabe3486ca3713eacda172a98.png)

### Attacks
+ [Nearly-zero cost attack scenario on Optimistic Rollup](https://ethresear.ch/t/nearly-zero-cost-attack-scenario-on-optimistic-rollup/6336)
+ [Non-attributable censorship attack on fraud-proof-based Layer2 protocols](https://ethresear.ch/t/non-attributable-censorship-attack-on-fraud-proof-based-layer2-protocols/6492)

