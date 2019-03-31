# Filecoin

* [一文看懂去中心化分布式存储 IPFS/FileCoin](https://mp.weixin.qq.com/s/sbZ_f8Q5jmkO8YHIijQJqQ)
* [IPFS/filecoin 技术架构分析](https://www.jianshu.com/c/06ddad58490f)

## IPFS协议

### IPFS vs BitTorrent
+ IPFS deduplicates across "torrents"
+ ipfs has trackerless peer discovery
    * magnet links have this too
+ ipfs can download part of a collection
+ ipfs has Pubsub
+ ipfs has mutiblity mechanisms
+ ipfs has proof of storage
    *  prove you have a compete file by only sending some of it
+ ipfs takes several good ideas from protocols like Bittorrent, Git, SFS, Bitcoin and the web
+ the main advantage is the fact that there is only a single massive swarm, where anybody can give a block to everybody.
    * for torrents, there is essentially a separate swarm for every torrent. Two big torrents with only a few small files differing won't share peers as IPFS does, for example.
    * the way Bittorrent chunks content into blocks doesn't work for all types of content (and isn't designed for deduplication between torrents).


+ https://github.com/ipfs/notes/issues/208
+ https://www.reddit.com/r/ipfs/comments/8qbgj3/whats_the_practical_difference_between_ipfs_and/
+ https://www.reddit.com/r/ipfs/comments/7gnqox/why_hasnt_ipfs_taken_over_the_torrents_yet/
+ https://www.reddit.com/r/ipfs/comments/a694jd/ipfs_vs_torrents_from_a_filtering_perspective/
+ https://medium.com/@kidinamoto/ipfs-vs-bittorrent-9f1c3adb8fcd
