# Block-chain安全服务平台调研测试报告

- 李儆卿



## 前提说明：

本次调研行为基于个人兴趣爱好。此文给出的评价结论均为个人主观意见，不涉及任何商业目的行为，与其它任何公司、组织及个人均无关联。



区块链安全服务平台一共提供五大类功能：

![](.\Image\platform_1.png)

此次调研对区块链安全服务平台提供的

###### [离线插件版智能合约安全检测工具]

###### [在线一键式智能合约自动验证工具]

两大功能进行了免费版试用， 未涉及收费企业版测试。至于当前无甚关联的平台其余功能模块，仅作浏览，亦未深入调研。

------

1.调研测试结论概要：

快速浏览结论，无需关注下面技术细节：



## 2. 支持的智能合约语言类型

根据产品说明，该平台当前只针对以太坊(Ethereum，以下可简称ETH)智能合约进行安全检测和审计报告。

测试中发现<u>**离线版和在线版两种工具核心并无区别**</u>，针对智能合约代码进行代码规范、语法错误、安全特性的扫描检测。



以太坊官方当前主要支持的智能合约语言为Solidity和Vyper：

[Smart Contract Languages](https://ethereum.org/developers/#smart-contract-languages)

*Any program that runs on the Ethereum Virtual Machine (EVM) is commonly referred to as a “smart contract”. The most popular languages for writing smart contracts on Ethereum are **Solidity** and **Vyper**, though there are [others under development](https://github.com/ConsenSys/ethereum-developer-tools-list#smart-contract-languages).*

实际测试中发现平台仅支持Solidity语言的智能合约安全检测

![](.\Image\online_checking_1.png)

尝试进行了Vyper智能合约的检测，检测工具提示编译失败，视其为不合法代码。

![](.\Image\online_checking_2.png)

### 结论：

#### <font color = d2b48c>**此安全检测工具受限于区块链类型（以太坊）及合约类型（Solidity），在泛用性方面参考价值有限。**</font>

------



## 2.支持的安全检测内容

参见免费版、企业版和人工审计检测内容比较图：

![](.\Image\compareversion.png)

------



## 3.具体测试

#### 局限：

​    由于安全检测服务平台受限于合约语言，调研过程中未对检测内容做全覆盖测试。

#### 测试方法：

​    学习Solidity特性，编写带有特定漏洞的智能合约。采用离线插件版工具对合约进行检测，观察行为及反馈。

​    作为对比，同时也使用在线检测工具进行扫描，并发现平台存在的部分Bug，详见以下案例。



### 案例1

#### 覆盖内容：

|     类型     |                检测项                |
| :----------: | :----------------------------------: |
| 代码规范检测 |       Tx Origin Authentication       |
| 函数调用检测 |        Invoke low level Calls        |
|              | Unchecked Call or send Return values |

#### 说明：

<font color = d2b48c size = 4 face="calibri">Tx Origin Authentication </font>

```Text
tx Origin 攻击:

tx Origin是solidity的一个状态变量，针对其可进行网络钓鱼攻击，耗尽所有资金。特点为: 受害者在智能合约中使用tx.origin检查合同所有者的身份.
```



<font color = d2b48c size = 4 face="calibri">Invoke low level Calls </font>

```Text
Invoke low level Calls:

call是Solidity提供的底层函数，用来与外部合约或者库进行交互。在使用时，必须对调用参数的安全性进行判定，否则易造成合约漏洞。
```



<font color = d2b48c size = 4 face="calibri">Unchecked Call or send Return values </font>

```Text
Unchecked Call or send Return values:

在调用call/send函数后, 失败也不会直接抛出异常，如果不检查调用返回值，函数会继续执行，造成合约漏洞。
```



#### 智能合约漏洞代码：

```javascript
pragma solidity ^0.4.18;
contract TxOriginVTest {
    address owner;
    function SmartContractTest() public {
        owner = msg.sender;
    }
    function transferTo(address to, uint amount) public {
        require(tx.origin == owner);
        to.call.value(amount)();
    }
    function() payable public {}
}

```

#### 检测结果：

![](.\Image\txOrigin.png)

```Text
PID: 30236

start compile!
compile over!
start deploy!
deploy over!
start execute function name: SmartContractTest()
execute function name : SmartContractTest() end!
start execute function name: transferTo(address,uint256)
start execute function name: None
execute function name : transferTo(address,uint256) end!
execute function name : None end!



####### TX Origin Authentication #######
file: D:\Projects\Smart Contract\txOriginVT.sol
title: TX Origin Authentication
description: Consider using "msg.sender" in place of "tx.origin.
code: require(tx.origin == owner);
warningType: Warning
line: 8


####### Invoke Low Level Calls #######
file: D:\Projects\Smart Contract\txOriginVT.sol
title: Invoke Low Level Calls
description: Avoid using low-level function "call".
code: to.call.value(amount)();
warningType: Warning
line: 9


####### Unchecked Call Or Send Return Values #######
file: D:\Projects\Smart Contract\txOriginVT.sol
title: Unchecked Call Or Send Return Values
description: Please check the return value of call or send
code: to.call.value(amount)();
warningType: Warning
line: 9



---end---
```

结论：

离线插件版成功地发现并汇报了合约中存在的三种漏洞。

使用在线版的扫描检测，未能发现 tx Origin 安全漏洞

![](.\Image\txOrigin_online.png)



### 案例2



