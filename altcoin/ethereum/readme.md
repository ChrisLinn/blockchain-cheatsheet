# Ethereunm

## Smart Contract 智能合约开发
+ 以太坊外部账户和合约账户的差别？ 

## Gas

## ERC20, ERC721

## Raiden Network 雷电网络
+ 多对一
+ 每个通道都需要部署一个新合约

雷電網路和閃電網路有幾點不同：

+ Alice 需要先把幣存入智慧合約，用來支付給 monitoring service 的費用。
+ 雷電網路的 monitoring service 知道每筆交易的通道 ID 和使用者地址，所以可以把同一個通道的狀態更新給關聯起來。
+ 雷電網路的 monitoring service 只能暫停 Bob 錯誤的提款，沒辦法直接幫 Alice 發起一筆交易來領走所有錢懲罰 Bob，必須 Alice 自己上線處理。
+ 不同於閃電網路，雷電網路裡作弊的人不會被罰錢。

## The DAO