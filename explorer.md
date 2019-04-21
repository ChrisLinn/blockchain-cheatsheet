# Explorer

## database
+ [bitcoin-abe](https://github.com/bitcoin-abe/bitcoin-abe)
+ [Blockstream/esplora](https://github.com/blockstream/esplora)
+ update logic
    ```
    chain_height = get_chain_height()  
    db_height = get_db_height()
    if db_height > -1
        db_height = verify_db_data(db_height)
    while db_height < chain_height
        db_height += 1
        new_block = get_block(db_height)
        new_block = verify_chain_data(new_block)
        store_block(new_block)
        db_height = get_db_height()
    ```
+ store_block
    * 对于一个 block:
        ```
        1.存 txs
            2.txs 中tx，存 input & output
                3.更新 token
                4.更新 数额
        5.存 raw_block
        ```
+ 故障回滚
    * verify_db_data(height)
        - if block(height+1) marked as orphan in db?
            + if yes, rollback_db_data_height(height+1)
        - find txs in block(height+1)
            + rollback_db_data_txs(txs)
        - while height > -1
            + db_block = get_db_block(height)
            + chain_block = get_block(height)
            + if db_block.preBlkHash != chain_block.preBlkHash
                * rollback_db_data_height(height)
                * height--
    * verify_chain_data(height)
        - 每请求一个区块的数据
            + 拿到数据后，校验这个区块的previous_block_hash跟数据库中高度-1的区块的hash是不是相等
            + 相等的话同步这个区块的数据
            + 不相等就回滚 db 高度-1 的区块然后再请求 高度-1 的区块
            + 递归的过程
    * rollback_db_data_height(height)
        - mark height orphan_block
        - txs = get_db_txs(height)
        - rollback_db_data_txs(txs)
            + update balance accoring to timestamp
            + update token accoring to timestamp
            + rollback_db_data_txs only marks txs as orphan, but will delete txs if called by verify_db_data 
+ 读写效率
+ 网络难度、算力变化
+ 持仓排名
+ 大宗交易

## 交易追踪

通过脚本把这些后续的交易都追踪出来, 重点关注:

1. 后续流出中 出现次数比较多的地址
2. 后续流出中 历史总交易次数比较多的地址
3. 后续流出中 交易金额比较大的地址
4. 后续流出中 地址余额比较大的地址

1, 2 是用于追踪如果他拆分混淆再归集到某个主地址，但 2 不仅仅关注后续的交易，还关注所有的历史交易次数，防止有多个小号