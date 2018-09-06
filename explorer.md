# Explorer

+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ 存储顺序
    * 对于一个 block:
        1. 存 txs
            2. txs 中tx，存 input & output
                3. 更新 token
                4. 更新 数额
        5. 存 block
+ 故障回滚
    * 区块 回滚思路 举例:
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚高度-1的区块然后再请求高度-1的区块
            + 递归的过程
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易
