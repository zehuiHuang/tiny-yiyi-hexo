---
title: solidity常用关键词解释理解
date: 2018-09-03 03:02:57
tags: [solidity]
categories: 以太坊
copyright: true
---

>solidity常用关键词解释理解:本篇文章主要介绍了solidity语法常用的关键词

- **import "./filename.sol"（导入合约）**
>类似JS的Import语句，这个声明导入了“filename”源码中所有的全局变量（以及在filename中导入的其他文件的变量）

- **contract（合约关键词）**
>每个合约都会有，相当于java 的class关键词
<!-- more -->
- **关于这四个可见、限制类型**
>external: 外部函数是合约接口的一部分,这意味着它们可以从其他合约调用, 也可以通过事务调用。外部函数f不能被内部调用(即 f()不执行,但this.f()执行)。外部函数，当他们接收大数组时，更有效。
public:公共函数是合约接口的一部分,可以通过内部调用或通过消息调用。对公共状态变量而言，会有的自动访问限制符的函数生成(见下文)。
internal:这些函数和状态变量只能内部访问(即在当前合约或由它派生的合约),而不使用（关键字）this 。
private:私有函数和状态变量仅仅在定义该合约中可见， 在派生的合约中不可见。

- **struct（结构体）**
>相当于java的面向对象编程的实体对象


- **modifier(修改器)**
>相当于java的在进入service层有一个逻辑判断（返回false or true），写在方法 可见类型和返回值之间，在调用方法时，会首先进入修改器，在修改器中，如果能走到 _ 这个占位符时，代码会进入方法体（如果在修改器中的 _ 占位符之后还有代码，执行完方法体里的代码后还会重新进入修改器，执行 _ 占位符后面的代码），如果代码没有走到_占位符则相当于代码中断，返回值就是返回类型的默认值。
- **delete（操作符）**
>delete a将对应类型的初始值赋值给a，即相当于a= 0。
对于静态数组使用delete时，是将数组内所有元素置为初值。而对于动态数组时，则是将长度置为0。对于结构体使用delete会将其中的所有成员变量一一置为初值。
delete对mapping没有效果（因为映射的密钥可以是任意的，并且通常是未知的）。因此，如果delete一个struct，将重置所有不是映射的mapping，也会递归到成员中，除非它们是映射。不过，单独的键和键对应的值可以被delete。



- **constant、view和pure（用来修饰函数的关键词）**
>view是只能读取storage变量的值，不能写入，pure是不能对storage变量进行读写constant是由Solidity v4.17之前的版本拆成了view和pure。view的作用和constant一模一样，可以读取状态变量但是不能改；pure则更为严格，pure修饰的函数不能改也不能读状态变量，只能操作函数内部变量（否则编译的时候就会报错）。
在web3js调用时，有此参数会告诉web3js这是eth_call而非eth_sendTransaction，这样就不会产生交易，也不会消耗gas。
- **function（方法体）**
>就相当于java中class的方法（外部每次调用一次方法，相当于一次交易，会被保存到区块上），写法上也和java略有不同：function +方法名（参数类型，参数类型，..）+可见性关键词（上面讲的四类）+（可以是修改器modifier、修饰器pure、view）+returns(返回参数) { return 返回值}
总结就是：function (<parameter types>) [private|public|internal|external][modifier][pure|constant|view|payable] [returns (<return types>)]
- **assert() 、require() 、 revert() （错误处理模式）**
> require一般是用在 确认条件参数、有效值等判定等地方，验证的一般都是考虑到代码的健壮性，如果触发会将剩余的gas返还给用户
revert和requeire几乎一样，一般用在有if else的逻辑比较复杂的代码中
assert一般用于预防不应该发生的情况，但不应该使条件错误的地方，并且会消耗gas，如果触发表示代码出现了漏洞。

-  **emit（事件标志词）**
>一般是为了区分方法和事件而后期引进的一个关键词
-  **Evnet（事件）**
>触发一个事件需要使用emit关键词，事件是与EVM日志设施的方便接口，在emit事件时，会把事件的参数数据存储到区块上。在合约内部任何人都无法访问，在合约外部可以通过监听的方式监听即将要触发的事件，也可以查看该事件的所有日志信息

- **memory/storag（引用类型）**
>Storage是指针传递，如果修饰是用Storage，在修改用Storage修饰的变量时，修改的是区块链上的存储
memory是值传递，如果修饰是用memory，在修改用memory修饰变量时（这个变量在被赋值时就会申请一个副本），修改的只是临时的副本文件，不会修改区块链的存储，副本文件在完成他的生命周期（方法执行完）后会被删除掉
即：Storage 变量是指永久存储在区块链中的变量。 Memory 变量则是临时的，当外部函数对某合约调用完成时，内存型变量即被移除。




