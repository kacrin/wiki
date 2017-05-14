---
title: "BTC"
layout: page
date: 2017-5-14 23:00
---

# BTC

比特币（英语：Bitcoin[注 2]）是一种全球通用的互联网加密货币.[wikipedia](https://zh.wikipedia.org/wiki/比特币)

## 钱包

Multibit（云数据区块功能）	http://multibit.org/	MIT
Bitcoin Core（官方客户端、核心客户端）	https://bitcoin.org/en/download	MIT
Bitoex币托（在线钱包）	https://bitoex.com	专有软件
My Wallet（在线钱包，on-chain）	https://blockchain.info/wallet	专有软件
Coinbase（在线钱包，混合式）	http://coinbase.com	专有软件
Electrum	http://electrum.ecdsa.org/	GPL
Armory（具有离线支付功能）	http://bitcoinarmory.com

## 挖矿产出

除了将接收到的交易信息打包到数据块，每个数据块都会允许发行一定数量的新比特币，用来激励成功发现数据块的矿工。比特币系统按照预定的货币增发节奏决定发行的比特币数量。如果其他支付交易有给手续费的，那么矿工还会获得手续费。由于矿工可以自行决定是否将某一个交易数据打包到数据块中，因此矿工有可能优先选择手续费较高的交易来打包。数据块产生速率的预期为每10分钟一个，但每个数据块中新发行的比特币不能超过50个，而这个数字每产出21万个区块，时间上约每隔4年就会减半，因此比特币的总数量不会超过2100万个。随着新发行比特币数量的下降，手续费将成为挖矿的主要动机。

## 矿池

在全网算力提升到了一定程度后，过低的获取奖励的概率。促使一些bitcointalk上的极客开发出一种可以将少量算力合并联合运作的方法，使用这种方式创建的网站便被称作“MiningPool”

中国矿池
[鱼池 f2pool](https://www.f2pool.com/help)
[蚂蚁矿池](ttps://www.antpool.com)

[蚂蚁矿池使用教程](http://www.wanbizu.com/baike/201512145944.html)
蚂蚁矿池必须使用 蚂蚁矿机 才可以挖矿

## 矿工挖矿模式

(a)PPLNS(0手续费)矿池挖到新块且块得到3个确认后，根据过去2.5个难度周期提交的工作量占总工作量的比例，对块收益25 BTC进行分配，此模式适合长期在蚂蚁矿池的矿工，收益会在矿机运行一段时间周期时根据工作量支付。
(b)PPS(2.5%手续费)收益=25 BTC*工作量/当前网络难度，此模式每天的固定时间会支付收益。适合在各矿池进行切换的矿工。
(c)SOLO(1%手续费)矿池挖到的新块为用户挖出且块得到3个确认后，将块收益25 BTC全部分配给用户 (块收益中交易费部分不进行分配，用于系统开发维护费用)，如果用户未挖到新块，则没有收益，此模式全靠运气成分。
(d)P2P(0手续费)P2P挖矿模式是继承p2pool开源项目而推出的挖矿模式，其为矿工提供了一种点对点的挖矿方式，防止算力集中在某个中心化的矿池而对比特币网络进行51%攻击。此模式需要自行搭建节点，可到这里下载相关程序：https://github.com/bitmaintech/p2pool

# ETH

以太坊 

# 矿池搭建

[BTC矿池搭建 - 初级](http://8btc.com/article-1803-1.html)
[BTC矿池搭建 - 高级](http://8btc.com/article-1804-1.html)

[方舟 挖矿站 p2pool deibian矿池搭建](http://www.fz49.com/thread-336-1-1.html)