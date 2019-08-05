# PR 学习
https://bitcoin-core-review-club.github.io 学习整理, 带你入门 bitcoin PR 的思维学习，培养给 bitcoin 提 PR 的能力。

## #15505
## #15713

## #15169 

Parallelize CheckInputs() in AcceptToMemoryPool() (mempool)

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

