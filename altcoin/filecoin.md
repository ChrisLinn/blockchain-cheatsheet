# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议
+ https://github.com/ipfs/research-CRDT
+ https://github.com/ipfs/specs/tree/master/bitswap
    * https://github.com/heems/bssim
+ https://github.com/dedis/student_19_libp2p_tlc

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a complete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
