---
title: 智能合约开发环境搭建、编写、编译、debug调式
date: 2018-09-03 23:38:34
tags: [智能合约 以太坊]
categories: 区块链
copyright: true
---
# 安装npm和nodejs
在**[以太坊私有链搭建](http://www.tiny-yiyi.com/2018/09/02/%E4%BB%A5%E5%A4%AA%E5%9D%8A%E7%A7%81%E6%9C%89%E9%93%BE%E6%90%AD%E5%BB%BA/#more)**文章中有该步骤方法

# truffle安装
+ 安装truffle工具
```
npm install -g truffle
```
# 新建truffle项目
可以[参考文章](https://truffleframework.com/docs/truffle/getting-started/creating-a-project)
```
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin
```
> metacoin是truffle已经存在的一个box，你也可以新建其他box，box其实就是一个可以直接发布的合约模板，包含了项目的源码和测试脚本等文件

<!-- more -->

+ 你也可以创建一个空的项目：
```
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin
```
+ 执行上面命令后会生成至少三个文件夹：

>contracts ：存放合约solidity源码
    migrates ：存放js写的迁移脚本,帮助你发布合约
    test ：存放js写的测试脚本

# 在contracts文件夹中新建myContracts.toml智能合约文件
下面是一个网上随便找到的一个简单的用solidity语言写的投票合约，具体的一些关键词理解可以在之前的文章[solidity常用关键词解释理解 ](http://www.tiny-yiyi.com/2018/09/02/solidity%E5%B8%B8%E7%94%A8%E5%85%B3%E9%94%AE%E8%AF%8D%E8%A7%A3%E9%87%8A%E7%90%86%E8%A7%A3/#more)查看
```
pragma solidity 0.4.24;
contract Votelihe {
   struct Candidate {
       uint votecount;
       string name;
   }
   struct Voter {
       bool voted;
   }
   mapping(address => Voter) public voters;
   Candidate[] public candidates;
   constructor () public {
     candidates.push(Candidate({
              name: "lihe",
              votecount: 0
         }));
     candidates.push(Candidate({
              name: "dandan",
              votecount: 0
         }));
   }
   function Vote_candidate(uint8 numCandidate) public
   {
       if(voters[msg.sender].voted ||numCandidate>candidates.length)return;
       candidates[numCandidate].votecount+=1;
       voters[msg.sender].voted=true;
   }
   function Getcount() public view returns(string,uint,string,uint)  {       return(candidates[0].name,candidates[0].votecount,candidates[1].name,candidates[1].votecount);
   }
}

```

# 迁移脚本
可以参考[文章](https://truffleframework.com/docs/truffle/getting-started/running-migrations)
在migrates文件夹下面新建一个2_deploy_contracts.js的文件（固定写法，在第一次发布合约时，会按照数字的前后顺序初始化），里面写上发布合约的内容（相当于发布合约），内容基本是固定的：
```
var Votelihe = artifacts.require("Votelihe");
module.exports = function(deployer) {
  // deployment steps
  deployer.deploy(Votelihe,参数1,参数2,...);//参数是在实例化合约时的构造函数需要的初始化参数
  ...//可以多个合约一起实例化
};
```
# truffle自带vm，启动truffle自带的虚拟机，会进入truffle命令行
```
truffle develop
```
执行命令后会进入truffle命令行，并且会默认给你十个用户公钥和私钥，可以用来测试，
```
Truffle Develop started at http://127.0.0.1:9545/

Accounts:
(0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
(1) 0xf17f52151ebef6c7334fad080c5704d77216b732
(2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
(3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
(4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
(5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
(6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
(7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
(8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
(9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

Private Keys:
(0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
(1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
(2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
(3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
(4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
(5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
(6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
(7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
(8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
(9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat

⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
Ensure you do not use it on production blockchains, or else you risk losing funds.
truffle(develop)> 
```
# 可以在truffle命令行执行web3.js的语法
> 用的是比较低的版本，目的是新的版本有很多异步，和命令行执行操作违背

比如执行查询用户的账户钱数：
```
web3.eth.getBalance("0x627306090abab3a6e1400e9345bc60c78a8bef57")
BigNumber { s: 1, e: 20, c: [ 1000000 ] }
```

# 译打包智能合约
直接在truffle命令行执行 compile 即可。

# 自动发布合约
> 在自动发布合约时，才会会用到之前编写的脚本迁移，直接执行 migrate 即可。如果你不是第一个使用migrate 需要执行命令： migrate --reset  

```
truffle(develop)> migrate --reset 
Using network 'develop'.

Running migration: 1_initial_migration.js
  Replacing Migrations...
  ... 0xb471f6087aee23e8d2922819f0561c42a5fae69a9341bb7a0e28a321c239acfe
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts...
Running migration: 2_initial_migration.js
  Replacing Votelihe...
  ... 0x4b927462b922b5c4fdea3a33a1345e9a447d287bdcd110ef09ef596a3d44b97b
  Votelihe: 0x345ca3e014aaf5dca488057592ee47305d9b3e10
Saving successful migration to network...
  ... 0xf36163615f41ef7ed8f4a8f192149a0bf633fe1a2398ce001bf44c43dc7bdda0
Saving artifacts...
truffle(develop)> 
```

# 调试合约
可以参考[文章](https://truffleframework.com/docs/truffle/getting-started/package-management-via-npm)
```
var contract = web3.eth.contract(Votelihe.abi)
var contractInstance = contract.at(Votelihe.address)
contractInstance.Vote_candidate(0,{from:web3.eth.accounts[0]})
```
执行之后返回了一个交易hash值（至于这个交易hash有啥用，后面会讲到）
```
truffle(develop)> contractInstance.Vote_candidate(0,{from:web3.eth.accounts[0]})
'0xb30ffb0e09845ea5724e72a2753369009059988b5c8d1278e8ce076c3ca601dc'
truffle(develop)> 
```
> 注意：Vote_candidate就是在合约中自己写的方法，最后一个参数是truffle中的web3封装了一层的参数，表示发起合约的账户私钥

然后在执行合约中另外一个方法Getcount（），查询一下是否投过一次票
```
truffle(develop)> contractInstance.Getcount()
[ 'lihe',
  BigNumber { s: 1, e: 0, c: [ 1 ] },
  'dandan',
  BigNumber { s: 1, e: 0, c: [ 0 ] } ]
truffle(develop)>
```
> 可以看到叫lihe的已经投票成功了，还差dandan还没有投票，当然，这个投票权限可以在合约初始化的时候做限制，咱们没有做而已。

用另外的帐号在执行投票一次
```
truffle(develop)> contractInstance.Vote_candidate(1,{from:web3.eth.accounts[1]})
'0x160a072fad7efb4b0cc11014e87942b804e61dab939dc15d77efbd1f9f1af282'
truffle(develop)> 
```
也是返回了一个交易hash，然后在执行查询操作看投票情况
```
truffle(develop)> contractInstance.Getcount()
[ 'lihe',
  BigNumber { s: 1, e: 0, c: [ 1 ] },
  'dandan',
  BigNumber { s: 1, e: 0, c: [ 1 ] } ]
truffle(develop)> 
```
很明显叫dandan的也投票成功。
现在说交易hash值的作用，它可以通过debug的方式调试这个已经完成的方法
```
truffle(develop)> debug 0xb30ffb0e09845ea5724e72a2753369009059988b5c8d1278e8ce076c3ca601dc
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/Votelihe.sol...

Gathering transaction data...

Addresses affected:
 0x345ca3e014aaf5dca488057592ee47305d9b3e10 - Votelihe

Commands:
(enter) last command entered (step next)
(o) step over, (i) step into, (u) step out, (n) step next
(;) step instruction, (p) print instruction, (h) print this help, (q) quit
(b) toggle breakpoint, (c) continue until breakpoint
(+) add watch expression (`+:<expr>`), (-) remove watch expression (-:<expr>)
(?) list existing watch expressions
(v) print variables and values, (:) evaluate expression - see `v`

Votelihe.sol:
1: pragma solidity 0.4.24;
2: contract Votelihe {
   ^^^^^^^^^^^^^^^^^^^
Votelihe.sol:
38:    }
39: 
40:    function Vote_candidate(uint8 numCandidate) public
       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
debug(develop:0xb30ffb0e...)> 
```
类似于IED的debug可以一步一步调式自己写的每行代码和参数值，这里有几个关键词，比如 v 是显示执行到该步骤时所有的变量值，可以通过 h 查看其他的作用。

打完手工～～
