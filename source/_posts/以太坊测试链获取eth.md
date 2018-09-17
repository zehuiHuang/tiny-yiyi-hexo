---
title: 以太坊测试链获取eth
date: 2018-09-05 22:13:29
tags: [以太坊 测试链]
categories: 区块链
copyright: true
---

>首先要先解释为什么要在坊的测试网络环境上部署测试你的合约，因为不同的环境虚拟机包括共识算法、实习方式可能都是不同的，所以在把合约部署到主网络之前，各个环境是都需要测试一下。当然在主网络上也得测试（因为主网络的代币是很贵的，白花花的银票啊，所有把所有可能出现的bug在上主网络之前尽量排除掉）
# 以太访的常用测试链
+ Ropsten https://ropsten.etherscan.io/ : POW共识机制算法，测试时需要消耗代币，可以自己建个节点挖矿赚取以太比，但是也没必要（测试链上的以太比没有实际价值），直接申请就可以。申请[地址](https://faucet.ropsten.be/)。
+ Kovan https://kovan.etherscan.io/ : POA共识机制算法，不能通过挖矿的方式获取以太比，测试时需要消耗代币，只需要申请即可，申请[地址](https://faucet.kovan.network/)。
+  Rinkeby https://rinkeby.etherscan.io/ : POA共识机制算法，同样不能通过挖矿的方式获取以太比,测试时需要消耗代币，申请[地址](https://faucet.rinkeby.io/)。
<!-- more -->
## Rinkeby获取以太比（网络需要翻墙）（开发人员最常用的）
>为了确保不会出现有人滥用，无限生成Rinkeby以太币，想获取Rinkeby测试网络中的以太币，需要去申请，这个申请Rinkeby以太币的功能被称为水龙头(Faucet),且申请时需要和某些帐号平台关联，目前支持Twitter、Facebook、google+三种。我用的时google+,没有谷歌帐号伙伴们去注册个吧。进入google+ [平台地址](https://plus.google.com/),直接像QQ发布一个说说一样，把说说的内容写成你的账户地址即可，然后自己分享一下
，最后点击 类似 [此信息最初是由zehui huang分享的](https://plus.google.com/106961385496275574669/posts/2CjXFEGsVWK) ，把该地址的链接放到Rinkeby的申请地址上输入框中，点击  *Give me Ether*  即可获得。可以通过[Rinkeby](https://rinkeby.etherscan.io) 查询出交易记录和获得的Ether。
## Ropsten获取以太比
Ropsten的获取级比较简单了，直接在[Ropsten](https://faucet.ropsten.be/)页面的输入框输入自己的账户地址即可获得
## Kovan获取以太比
### 索取的方式
这个挺有意思,可以通过[地址](https://gitter.im/kovan-testnet/faucet)来索取，通过一个聊天输入框，发给他你的账户地址，有人会校验你的地址并发给你eth：

```
zehuiHuang
@zehuiHuang
09:40
0x689b9755660ea2b045c66d3fdf33df3e3b3ba9bf
Scott Bigelow
@epheph
09:41
@zehuiHuang sent 3.0 eth to 0x689b9755660ea2b045c66d3fdf33df3e3b3ba9bf: https://kovan.etherscan.io/tx/0xa86a361c5ec6a4795397118a02ce0dd136da65e760fafca13aff04c0620cd096
REMINDER: this is NOT real (mainnet) ETH, and has no market value. It is only useful for testing
zehuiHuang
@zehuiHuang
09:41
thanks 
```
### 自动获取的方式（24小时一次）
通过地址 https://github.com/kovan-testnet/faucet ，在README.md里有 自动获取eth的地址，需要用到gitHub账户登录。
![图片测试](以太坊测试链获取eth/weixin.jpg)
