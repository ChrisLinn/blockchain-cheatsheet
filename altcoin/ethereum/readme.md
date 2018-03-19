# Ethereunm

## Smart Contract 智能合约开发

## Gas
以太坊中 GAS 存在的问题

虽然有一个 STARTGAS 值来指定程序运行多少步，防止程序无限循环不结束。但仅靠这个机制还是不完美的，因为当超过了 STARTGAS 的值，交易被回滚，但挖矿费用 refund 还是要支付的，并且计算资源也被浪费了。

而且也可能存在 DoS with Block Gas Limit
上面提到 需要支付 refund，但如果 refund 函数中添加了很多 refund 地址，gas cost of refunding 大于 gas limit，那么 refund 交易也会被取消，没有人能得到 refund。

## ERC20, ERC721

## Raiden Network