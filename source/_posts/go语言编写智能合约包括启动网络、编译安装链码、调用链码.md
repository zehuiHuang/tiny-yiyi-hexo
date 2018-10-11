---
title: go语言编写智能合约包括启动网络、编译安装链码、调用链码
date: 2018-09-18 19:09:28
tags: [Fabric go] 
categories: Hyperledger
copyright: true
---

>  Hyperledger Fabric编写智能合约目前主流是用go语言写的，关于go语言的基本语法和使用我会专门再写几章来介绍。该案例是在fabric-samples项目的基础上，来进行启动网络、编译安装链码、调用链码的。
>  当然在这之前还得安装好本地的Hyperledger Fabric开发环境(包括GO、Docker、镜像，jdk等)，本地开发环境参考地址：[快来点我](http://www.tiny-yiyi.com/2018/09/18/Hyperledger%20Fabric%20%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA/#more)
>  项目都是在终端操作，并且需要启动三个终端。
# 在fabric-samples中创建go文件，编写链码（智能合约）
```
cd ～
git clone https://github.com/hyperledger/fabric-samples.git
git checkout release-1.0
cd  ～/fabric-samples/chaincode
mkdir mychaincode
cd mychaincode
vim mychaincode.go
```
<!-- more -->
> mychaincode.go的合约内容：
```
//这里是一个简单资产ChainCode的编码。关于源码的解读，写在了注释里面。
// This is my first ChainCode
/*
 * 引入相关的包
 */

package main

import (
	"fmt"

	"github.com/hyperledger/fabric/core/chaincode/shim"
	"github.com/hyperledger/fabric/protos/peer"
)

// 定义一个简单资产结构体
type SimpleAsset struct {
}


//为SimpleAsset结构体实现Init接口函数。这个函数会在ChainCode初始化，或者升级的时候调用。
func (t *SimpleAsset) Init(stub shim.ChaincodeStubInterface) peer.Response {
	// 从交易提案中获取参数
	args := stub.GetStringArgs()
	if len(args) != 2 {
		return shim.Error("Incorrect arguments. Expecting a key and a value")
	}

	//通过 stub.PutState() 来讲数据写入到账本中。

	// 将key-value键值对写入到账本中。
	err := stub.PutState(args[0], []byte(args[1]))
	if err != nil {
		return shim.Error(fmt.Sprintf("Failed to create asset: %s", args[0]))
	}
	return shim.Success(nil)
}


//当发起交易时，会调用Invoke接口函数。
func (t *SimpleAsset) Invoke(stub shim.ChaincodeStubInterface) peer.Response {
	// 从交易提案中获取要调用的功能函数名。
	fn, args := stub.GetFunctionAndParameters()

	var result string
	var err error
	if fn == "set" {
		result, err = set(stub, args)
	} else { // assume 'get' even if fn is nil
		result, err = get(stub, args)
	}
	if err != nil {
		return shim.Error(err.Error())
	}

	// Return the result as success payload
	return shim.Success([]byte(result))
}


// Set功能函数，用于将数据写入到账本中。
func set(stub shim.ChaincodeStubInterface, args []string) (string, error) {
	if len(args) != 2 {
		return "", fmt.Errorf("Incorrect arguments. Expecting a key and a value")
	}

	err := stub.PutState(args[0], []byte(args[1]))
	if err != nil {
		return "", fmt.Errorf("Failed to set asset: %s", args[0])
	}
	return args[1], nil
}

// Get 功能函数，用于查询账本中函数的值。
func get(stub shim.ChaincodeStubInterface, args []string) (string, error) {
	if len(args) != 1 {
		return "", fmt.Errorf("Incorrect arguments. Expecting a key")
	}

	value, err := stub.GetState(args[0])
	if err != nil {
		return "", fmt.Errorf("Failed to get asset: %s with error: %s", args[0], err)
	}
	if value == nil {
		return "", fmt.Errorf("Asset not found: %s", args[0])
	}
	return string(value), nil
}


//main函数，用于在实例化过程中，在容器中启动ChainCode。
func main() {
	if err := shim.Start(new(SimpleAsset)); err != nil {
		fmt.Printf("Error starting SimpleAsset chaincode: %s", err)
	}
}

```
# 启动网络-终端1
 > 首先进入～/fabric-samples/chaincode-docker-devmode目录并执行如下命令，注意：由于之前使用过1.2.0版本，重新加载过镜像导致出现memoryerror docker-compose returned -1的错误，
 > 解决办法就是执行 docker rmi -f $(docker images -a -q) 删除掉所有的镜像，重新在1.0.0版本下 下载镜像即可
 
 ```
 docker-compose -f docker-compose-simple.yaml up
 ```

# 进入了cli容器-终端2

```
docker exec -it chaincode bash
```
在终端2中可以看到有之前创建过得mychaincode文件夹，进入mychaincode文件夹并编译go文件的合约
```
cd mychaincode
go build
```
编译之后会多出一个mychaincode文件，即编译好的合约

> 运行编译好的合约

```
$ CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:0 ./mychaincode
2018-09-18 09:37:15.211 UTC [shim] SetupChaincodeLogging -> INFO 001 Chaincode log level not provided; defaulting to: INFO
2018-09-18 09:37:15.211 UTC [shim] SetupChaincodeLogging -> INFO 002 Chaincode (build level: ) starting up ...

```
# 安装链码（智能合约）-终端3

```
docker exec -it cli bash
peer chaincode install -p chaincodedev/chaincode/mychaincode -n mycc -v 0
peer chaincode instantiate -n mycc -v 0 -c '{"Args":["a","10"]}' -C myc
```
在CLI内部会为mychaincode创建SignedChaincodeDeploymentSpec，并将其发送到本地peer节点。这些节点会调用LSCC上的Install方法。上述的-p选项指明chaincode的路径，其必须在用户的GOPATH目录下（比如$GOPATH/src/sacc）。
