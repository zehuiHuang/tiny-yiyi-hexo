---
title: Hyperledger Fabric 环境搭建
date: 2018-09-18 13:50:28
tags: Fabric 
categories: Hyperledger
copyright: true
---

## 首先安装好Ubuntu环境
## GO环境安装
```
$ wget https://storage.googleapis.com/golang/go1.9.linux-amd64.tar.gz
```
> 如果命令下载不下来,可先浏览器手动下载下来并解压，解压命令和路径为：

```
$ sudo tar -C /usr/local -xzf go1.9.linux-amd64.tar.gz
```

#### go环境变量设置
>  root用户下----命令：

```
$ vim  ~/.profile
 export PATH=$PATH:/usr/local/go/bin
 export GOROOT=/usr/local/go
 export GOPATH=$HOME/go
 export PATH=$PATH:$HOME/go/bin
$ source ~/.profile 
```

> 普通用户下-----命令

```
$ vim  /etc/profile
export PATH=$PATH:/usr/local/go/bin
$ source /etc/profile
```
<!-- more -->

> 由于我们把go的目录GOPATH设置为当前用户的文件夹下，所以记得创建go文件夹 执行：

```
$ cd ~
$ mkdir go
```
> 检查go的环境变量是否生效,提示：stat test.go: no such file or directory 表示已经ok

```
$ go run test.go
stat test.go: no such file or directory
```
## 安装docker
```
$ curl -fsSL https://get.docker.com/ | sh  或者 curl -sSL https://get.daocloud.io/docker | sh
```
> 修改当前用户权限
```shell
$ sudo usermod -aG docker huangzehui  #huangzehui改成自己的当前用户名
```
>  添加阿里云的Docker Hub镜像（我自己的镜像）
```
$ sudo mkdir -p /etc/docker
$   sudo tee /etc/docker/daemon.json <<-'EOF'
  {
   "registry-mirrors": ["https://enw355y4.mirror.aliyuncs.com"]
  }
  EOF
$  sudo systemctl daemon-reload
$  sudo systemctl restart docker
  ```
  ## Docker-Compose的安装
  ```
 $ sudo apt-get install python-pip
 $ curl -L https://get.daocloud.io/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > ~/docker-compose
 $ sudo mv ~/docker-compose /usr/local/bin/docker-compose
 $ chmod +x /usr/local/bin/docker-compose
  ```
 ## Fabric源码下载（目前用的是1.0版本）
  
 ```
$ mkdir -p ~/go/src/github.com/hyperledger
$ cd ~/go/src/github.com/hyperledger
$ git clone https://github.com/hyperledger/fabric.git
$ cd ~/go/src/github.com/hyperledger/fabric
$ git checkout v1.0.0
  ```
 ## Fabric Docker镜像的下载
> 在下载镜像的时候可以用阿里云的,下载的比较快，可以手动修改/etc/apt/source.list，具体内容搜一下，修改之后执行

```
$ sudo apt-get update
```
> (在本次下载的时候有些包下载不下来404,)，可以在系统中设置-软件和更新，设置成主服务器，执行

```
$ sudo -apt-get update
$ cd ~/go/src/github.com/hyperledger/fabric/examples/e2e_cli/
$ source download-dockerimages.sh -c x86_64-1.0.0 -f x86_64-1.0.0
$ docker images
REPOSITORY                                   TAG                 IMAGE ID            CREATED             SIZE
network001-peer0.org2.example.com-mycc-1.0   latest              ef138d5fea4d        3 weeks ago         173MB
network001-peer0.org1.example.com-mycc-1.0   latest              b32725aaca43        3 weeks ago         173MB
hello-world                                  latest              2cb0d9787c4d        2 months ago        1.85kB
hyperledger/fabric-baseimage                 x86_64-0.4.7        390ac2e95bc7        5 months ago        1.41GB
hyperledger/fabric-ca                        x86_64-1.0.1        5f30bda5f7ee        13 months ago       238MB
hyperledger/fabric-tools                     x86_64-1.0.1        259847d24868        13 months ago       1.34GB
hyperledger/fabric-orderer                   x86_64-1.0.1        bbf2708c9487        13 months ago       179MB
hyperledger/fabric-peer                      x86_64-1.0.1        abb05def5cfb        13 months ago       182MB
hyperledger/fabric-ccenv                     x86_64-1.0.1        7e2019cf8174        13 months ago       1.29GB
hyperledger/fabric-tools                     latest              0403fd1c72c7        14 months ago       1.32GB
hyperledger/fabric-tools                     x86_64-1.0.0        0403fd1c72c7        14 months ago       1.32GB
hyperledger/fabric-couchdb                   latest              2fbdbf3ab945        14 months ago       1.48GB
hyperledger/fabric-couchdb                   x86_64-1.0.0        2fbdbf3ab945        14 months ago       1.48GB
hyperledger/fabric-kafka                     latest              dbd3f94de4b5        14 months ago       1.3GB
hyperledger/fabric-kafka                     x86_64-1.0.0        dbd3f94de4b5        14 months ago       1.3GB
hyperledger/fabric-zookeeper                 latest              e545dbf1c6af        14 months ago       1.31GB
hyperledger/fabric-zookeeper                 x86_64-1.0.0        e545dbf1c6af        14 months ago       1.31GB
hyperledger/fabric-orderer                   latest              e317ca5638ba        14 months ago       179MB
hyperledger/fabric-orderer                   x86_64-1.0.0        e317ca5638ba        14 months ago       179MB
hyperledger/fabric-peer                      latest              6830dcd7b9b5        14 months ago       182MB
hyperledger/fabric-peer                      x86_64-1.0.0        6830dcd7b9b5        14 months ago       182MB
hyperledger/fabric-javaenv                   latest              8948126f0935        14 months ago       1.42GB
hyperledger/fabric-javaenv                   x86_64-1.0.0        8948126f0935        14 months ago       1.42GB
hyperledger/fabric-ccenv                     latest              7182c260a5ca        14 months ago       1.29GB
hyperledger/fabric-ccenv                     x86_64-1.0.0        7182c260a5ca        14 months ago       1.29GB
hyperledger/fabric-ca                        latest              a15c59ecda5b        14 months ago       238MB
hyperledger/fabric-ca                        x86_64-1.0.0        a15c59ecda5b        14 months ago       238MB
hyperledger/fabric-baseos                    x86_64-0.3.1        4b0cab202084        16 months ago       157MB

```
  ## 启动Fabric网络
  ```
$ cd ~/go/src/github.com/hyperledger/fabric/examples/e2e_cli/
$ ./network_setup.sh up
  ```
> 执行后无报错，有如下的打印END-E2E的样式展示（如果报错比如找不到cli等应该时安装docker时没有把镜像下载全或者时docker没有正常安装）
```
2018-09-18 05:24:22.601 UTC [main] main -> INFO 007 Exiting.....
===================== Query on PEER3 on channel 'mychannel' is successful ===================== 

===================== All GOOD, End-2-End execution completed ===================== 


 _____   _   _   ____            _____   ____    _____ 
| ____| | \ | | |  _ \          | ____| |___ \  | ____|
|  _|   |  \| | | | | |  _____  |  _|     __) | |  _|  
| |___  | |\  | | |_| | |_____| | |___   / __/  | |___ 
|_____| |_| \_| |____/          |_____| |_____| |_____|


```
  > 关掉之前启动的Fabric网络命令
  
  ```
 $ ./network_setup.sh down
  ```
## 执行demo 案例
> 另外打开一个终端，进入docker的cli
  ```
 $ docker exec -it cli bash
  ```
 > 查询a账户余额90
 ```
$ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'

2018-09-18 05:30:07.933 UTC [msp] GetLocalMSP -> DEBU 001 Returning existing local MSP
2018-09-18 05:30:07.933 UTC [msp] GetDefaultSigningIdentity -> DEBU 002 Obtaining default signing identity
2018-09-18 05:30:07.933 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 003 Using default escc
2018-09-18 05:30:07.933 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 004 Using default vscc
2018-09-18 05:30:07.933 UTC [msp/identity] Sign -> DEBU 005 Sign: plaintext: 0A95070A6708031A0C08DF9A82DD0510...6D7963631A0A0A0571756572790A0161 
2018-09-18 05:30:07.933 UTC [msp/identity] Sign -> DEBU 006 Sign: digest: 5EB72538B0BD2043F5DCA75C8453F1842B44118C00E5EA2F48CF6B079B67E7CC 
Query Result: 90
2018-09-18 05:30:07.939 UTC [main] main -> INFO 007 Exiting.....

 ```
  > 查询b账户的余额210
  ```
$ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","b"]}'
2018-09-18 05:32:31.062 UTC [msp] GetLocalMSP -> DEBU 001 Returning existing local MSP
2018-09-18 05:32:31.062 UTC [msp] GetDefaultSigningIdentity -> DEBU 002 Obtaining default signing identity
2018-09-18 05:32:31.062 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 003 Using default escc
2018-09-18 05:32:31.062 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 004 Using default vscc
2018-09-18 05:32:31.062 UTC [msp/identity] Sign -> DEBU 005 Sign: plaintext: 0A94070A6608031A0B08EF9B82DD0510...6D7963631A0A0A0571756572790A0162 
2018-09-18 05:32:31.062 UTC [msp/identity] Sign -> DEBU 006 Sign: digest: 3B2C18CEF14EEB452AB9C3DE3473D90F8425B289B5E5DB20154C85F0E3B92D80 
Query Result: 210
2018-09-18 05:32:31.067 UTC [main] main -> INFO 007 Exiting.....

  ```
 >  转给b账户10
 ```
$ peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n mycc -c '{"Args":["invoke","a","b","10"]}'
 2018-09-18 05:34:13.714 UTC [msp] GetLocalMSP -> DEBU 001 Returning existing local MSP
2018-09-18 05:34:13.714 UTC [msp] GetDefaultSigningIdentity -> DEBU 002 Obtaining default signing identity
2018-09-18 05:34:13.722 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 003 Using default escc
2018-09-18 05:34:13.722 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 004 Using default vscc
2018-09-18 05:34:13.722 UTC [msp/identity] Sign -> DEBU 005 Sign: plaintext: 0A95070A6708031A0C08D59C82DD0510...696E766F6B650A01610A01620A023130 
2018-09-18 05:34:13.722 UTC [msp/identity] Sign -> DEBU 006 Sign: digest: 62A8A544F31A609B535F529436C58862246C2B492C54861CF9EF5D1739879538 
2018-09-18 05:34:13.730 UTC [msp/identity] Sign -> DEBU 007 Sign: plaintext: 0A95070A6708031A0C08D59C82DD0510...7E43FBA6958BF8CA5FB7DB587AB52E2E 
2018-09-18 05:34:13.730 UTC [msp/identity] Sign -> DEBU 008 Sign: digest: BB84700E38F4664E7DD39987E23114E2989E935EA6C889F992329F1364396536 
2018-09-18 05:34:13.735 UTC [chaincodeCmd] chaincodeInvokeOrQuery -> DEBU 009 ESCC invoke result: version:1 response:<status:200 message:"OK" > payload:"\n [$\263\360\350\204\307!\023\314\364\304[vs\230\277\244w7\304\352\313G\265\310<\341\3569l\227\022Y\nE\022\024\n\004lscc\022\014\n\n\n\004mycc\022\002\010\003\022-\n\004mycc\022%\n\007\n\001a\022\002\010\004\n\007\n\001b\022\002\010\004\032\007\n\001a\032\00280\032\010\n\001b\032\003220\032\003\010\310\001\"\013\022\004mycc\032\0031.0" endorsement:<endorser:"\n\007Org1MSP\022\200\006-----BEGIN -----\nMIICGTCCAb+gAwIBAgIQVBxcUcN2WzaEYmfgjYR8uzAKBggqhkjOPQQDAjBzMQsw\nCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNU2FuIEZy\nYW5jaXNjbzEZMBcGA1UEChMQb3JnMS5leGFtcGxlLmNvbTEcMBoGA1UEAxMTY2Eu\nb3JnMS5leGFtcGxlLmNvbTAeFw0xODA5MTgwNTI4MzVaFw0yODA5MTUwNTI4MzVa\nMFsxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1T\nYW4gRnJhbmNpc2NvMR8wHQYDVQQDExZwZWVyMC5vcmcxLmV4YW1wbGUuY29tMFkw\nEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEWTAcqEoI6sJeGhP8CXxq9GVTy619X/wi\nN61YP4R1cRwnnGgc4u4G9xjsTJK7Ey/l6SQfkVUN8q09JCbMIjfAPqNNMEswDgYD\nVR0PAQH/BAQDAgeAMAwGA1UdEwEB/wQCMAAwKwYDVR0jBCQwIoAgr2XSTohd8p6g\nL7ZpOOvyUeOA6aISbpEzeiE8m45bHukwCgYIKoZIzj0EAwIDSAAwRQIhALEUwDwS\nHzCx0e8HW0V26kLYo7DUcBAiffuzpcoUkZtOAiBrFcG/ALDHrbLWYNQNjJfw5buR\nQZmUeJ7ElX/beWVpJw==\n-----END -----\n" signature:"0E\002!\000\255\372\201\203.\257\267\017)\213\302\220'\024\240\243\206\325\242a\313w9&\367)\315\212Pe;\006\002 ^0\216\3678VdH\207\237Y\324\240H\t8~C\373\246\225\213\370\312_\267\333Xz\265.." > 
2018-09-18 05:34:13.735 UTC [chaincodeCmd] chaincodeInvokeOrQuery -> INFO 00a Chaincode invoke successful. result: status:200 
2018-09-18 05:34:13.735 UTC [main] main -> INFO 00b Exiting.....

 ```
  > 之后从新执行 查询a和b的余额会发现a的账户少了10，而b的账户多了10
  ```
$ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","a"]}'
2018-09-18 05:37:50.416 UTC [msp] GetLocalMSP -> DEBU 001 Returning existing local MSP
2018-09-18 05:37:50.416 UTC [msp] GetDefaultSigningIdentity -> DEBU 002 Obtaining default signing identity
2018-09-18 05:37:50.416 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 003 Using default escc
2018-09-18 05:37:50.416 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 004 Using default vscc
2018-09-18 05:37:50.416 UTC [msp/identity] Sign -> DEBU 005 Sign: plaintext: 0A95070A6708031A0C08AE9E82DD0510...6D7963631A0A0A0571756572790A0161 
2018-09-18 05:37:50.416 UTC [msp/identity] Sign -> DEBU 006 Sign: digest: 1F16E4FA549D11DE0630590FD64F3C3456815182C296BE2A57D16B668FA2F181 
Query Result: 80
2018-09-18 05:37:50.421 UTC [main] main -> INFO 007 Exiting.....

  ```
  ```
$ peer chaincode query -C mychannel -n mycc -c '{"Args":["query","b"]}'
2018-09-18 05:39:10.368 UTC [msp] GetLocalMSP -> DEBU 001 Returning existing local MSP
2018-09-18 05:39:10.368 UTC [msp] GetDefaultSigningIdentity -> DEBU 002 Obtaining default signing identity
2018-09-18 05:39:10.368 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 003 Using default escc
2018-09-18 05:39:10.368 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 004 Using default vscc
2018-09-18 05:39:10.368 UTC [msp/identity] Sign -> DEBU 005 Sign: plaintext: 0A95070A6708031A0C08FE9E82DD0510...6D7963631A0A0A0571756572790A0162 
2018-09-18 05:39:10.368 UTC [msp/identity] Sign -> DEBU 006 Sign: digest: AB2BEEDCE26459B3F9C5E5324C2CF6FFE8DD97073D98ACD26710C74A4D963495 
Query Result: 220
2018-09-18 05:39:10.373 UTC [main] main -> INFO 007 Exiting.....

  ```
  
