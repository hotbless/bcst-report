# Block-chain安全服务平台调研测试报告

- 李儆卿



## 前提说明：

本次调研行为基于个人兴趣爱好。此文给出的评价结论均为个人主观意见，不涉及任何商业目的行为，与其它任何公司、组织及个人均无关联。



区块链安全服务平台一共提供五大类功能：

![](.\Image\platform_1.png)

此次调研对区块链安全服务平台提供的两大功能进行了免费版试用， 未涉及收费企业版测试。至于当前无甚关联的平台其余功能模块，仅作浏览，亦未深入调研。

离线插件版智能合约安全检测工具

```Text
使用方式：

1. 安装Node.js，VC++ 2019库，VS Code
2. 在VS Code中，选择install additional debuggers, 选择平台提供的插件安装
3. 打开合约代码，插件选择编译器版本，并执行扫描检测
```



在线一键式智能合约自动验证工具

```Text
使用方式：

1. 复制粘贴合约代码，进行扫描检测
2. 上传代码文件进行读取，执行扫描检测
```

------





## 1.调研测试结论概要：

<font color = 8fbc8f>注：本节用于快速获取调研结论，节省时间。如需关注更多技术实现细节，请阅读2-4节部分</font>

### 产品定位

​       <font color = d2b48c>安全服务平台针对担心源代码泄漏的用户，推出离线插件版工具；又为持观望态度的潜在用户，提供快捷的在线版试用；在此基础上，通过提供功能扩展的企业版，以及人工审计服务，来进一步细分客户群体。</font>产品定位清晰，目标明确，值得借鉴。

### 试用结论

​       <font color = d2b48c>工具目前仅适用于以太坊solidity智能合约的安全扫描检测</font>。由于限定了区块链类型和合约语言，不具泛用性。当使用非solidity语言开发智能合约时，其参考价值有限。

​       <font color = d2b48c>实际使用过程中，发现离线插件版和在线运行版工具扫描结果存有不一致情况。</font>由于两者核心部分相同，且均存在一方探知漏洞而对方却无法检出的情况。因此，暂认为这种结果差异是工具自身问题，而非市场行为所致。

​       <font color = d2b48c>单就离线版而言，通过编写带漏洞的solidity智能合约提交检测，发现一部分声称已覆盖的检测项未能正确运作，使得合约中存在的某些漏洞未被发现。</font>由此，给个人带来了不佳的用户体验，暂认为工具尚有不少有待改进之处，同时也导致对收费的企业版完全功能存疑。

​       然而，他山之石可以攻玉。 <font color = d2b48c>安全服务平台归纳出的具体检测项种类，深入语言规范的思路，以及从以太坊实际黑客行为中总结的方法，很有学习意义。</font>当检测其它语言开发的智能合约时，也需对其特性和限制进行挖掘，结合区块链平台具体事例，才能开发出更完善、更具泛用性的安全工具。

------





## 2. 支持的智能合约语言类型

根据产品说明，该平台当前只针对以太坊(Ethereum，以下可简称ETH)智能合约进行安全检测和审计报告。

测试中发现**离线版和在线版两种工具核心并无区别**，针对智能合约代码进行代码规范、语法错误、安全特性的扫描检测。



以太坊官方当前主要支持的智能合约语言为Solidity和Vyper：

[Smart Contract Languages](https://ethereum.org/developers/#smart-contract-languages)

<font color = 8fbc8f>*Any program that runs on the Ethereum Virtual Machine (EVM) is commonly referred to as a “smart contract”. The most popular languages for writing smart contracts on Ethereum are **Solidity** and **Vyper**, though there are [others under development](https://github.com/ConsenSys/ethereum-developer-tools-list#smart-contract-languages).*</font>



实际测试中发现平台仅支持Solidity语言的智能合约安全检测

![](.\Image\online_checking_1.png)



尝试进行了Vyper智能合约的检测，检测工具提示编译失败，视其为不合法代码。

![](.\Image\online_checking_2.png)



### 结论：

此安全检测工具受限于区块链类型（以太坊）及合约类型（Solidity），在泛用性方面参考价值有限。

------





## 3.支持的安全检测内容

参见免费版、企业版和人工审计检测内容比较图：

![](.\Image\compareversion.png)

------





## 4.调研测试详述

### 局限：

​    由于安全检测服务平台受限于合约语言，参考价值有限，因此未对检测内容做全覆盖研究测试。

### 测试方法：

​    学习了解Solidity特性，自行编写带有特定漏洞的智能合约。采用离线插件版工具对合约进行检测，观察行为及反馈。

​    作为对比，同时也使用在线检测工具进行扫描，并发现平台存在的部分Bug，详见以下案例。



### 案例1

#### 覆盖内容：

|     类型     |                            检测项                            |
| :----------: | :----------------------------------------------------------: |
| 代码规范检测 |     <font color = daa520>Tx Origin Authentication</font>     |
| 函数调用检测 |      <font color = daa520>Invoke low level Calls</font>      |
|              | <font color = daa520>Unchecked Call or send Return values</font> |

#### 说明：

<font color = daa520 size = 4 face="calibri">Tx Origin Authentication </font>

```Text
tx Origin 攻击:

tx Origin是solidity的一个状态变量，针对其可进行网络钓鱼攻击，耗尽所有资金。特点为: 受害者在智能合约中使用tx.origin检查合同所有者的身份.
```



<font color = daa520 size = 4 face="calibri">Invoke low level Calls </font>

```Text
Invoke low level Calls:

call是Solidity提供的底层函数，用来与外部合约或者库进行交互。在使用时，必须对调用参数的安全性进行判定，否则易造成合约漏洞。
```



<font color = daa520 size = 4 face="calibri">Unchecked Call or send Return values </font>

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

#### 结论：

离线插件版成功地发现并汇报了合约中存在的三种漏洞。

使用在线版的扫描检测，未能发现<font color = daa520> tx Origin </font>安全漏洞

![](.\Image\txOrigin_online.png)



### 案例2

#### 覆盖内容：

|   类型   |                    检测项                     |
| :------: | :-------------------------------------------: |
| 溢出检测 | <font color = daa520>Integer Overflow</font>  |
|          | <font color = daa520>Integer Underflow</font> |

#### 说明：

<font color = daa520 size = 4 face="calibri">Overflow </font>

```Text
Integer Overflow and Integer Underflow:

Solidity为了节约gas, 具有对uint类型变量的低级控制特性。因此有存在上溢/下溢的漏洞可能。
```

#### 智能合约漏洞代码：

```javascript
pragma solidity 0.4.18;

// 测试整型溢出: overflow and underflow

contract OverflowUnderFlow {
    uint public zero = 0;
    uint public max = 2**256-1;
    
    // zero 将为 2**256-1
    function underflow() public {
        zero -= 1;
    }

    // max 将为 0
    function overflow() public {
        max += 1;
    }
}
```

#### 检测结果：

扫描工具正确检测了 <font color = daa520>Underflow</font> 漏洞存在，但未能查出 <font color = daa520>Overflow</font> 漏洞

```Text
PID: 12516

start compile!
compile over!
start deploy!
deploy over!
start execute function name: overflow()
start execute function name: underflow()
execute function name : overflow() end!
execute function name : underflow() end!

####### Integer Underflow #######
file: D:\Projects\Smart Contract\OverflowVT.sol
title: Integer Underflow
description: There may be an integer Underflow error.
code: zero -= 1;
warningType: Warning
line: 11

---end---
```

因此我修改了合约代码关于 <font color = daa520>Overflow </font>的函数部分，使其返回变量值

```javascript
function overflow() public returns (uint) {
        uint val = max + 1;
        return val;
}
```

再次运行，扫描工具发现了 <font color = daa520>Overflow</font> 漏洞，暂且认为这是工具存在的bug。

```Text
####### Integer Overflow #######
file: D:\Projects\Smart Contract\OverflowVT.sol
title: Integer Overflow
description: There may be an integer Overflow error.
code: uint val = max + 1;
warningType: Warning
line: 16
```



### 案例3

#### 覆盖内容：

|       类型       |                     检测项                     |
| :--------------: | :--------------------------------------------: |
| 业务逻辑安全检测 | <font color = daa520>Check This Balance</font> |

#### 说明：

<font color = daa520 size = 4 face="calibri">This Balance </font>

```Text
this.balance:

合约代码中严格限制了合约的资金，而合约资金是可变的，因此能利用这个特性使得合约的功能逻辑无法正常执行。比如，函数具有条件声明，执行取决于该合同的余额低于一定金额，则可能会绕过该声明。
```

#### 智能合约漏洞代码：

```javascript
pragma solidity 0.4.18;

contract ForceEtherVT {
    bool check = false;

    function ConditionalBalance() {
        require(this.balance > 0); 
        check = true;
    }

    function() payable {
        revert();
    }         
}
```

#### 检测结果：

遗憾的是，扫描工具未能检测出合约中的 <font color = daa520>this.balance</font> 漏洞，暂且认为是工具存在的bug。

![](.\Image\this_balance.png)



### 整体结论：

自行编写带漏洞的solidity智能合约，将其提交至安全检测工具进行扫描检测。一部分平台声称覆盖的合约漏洞能够被发现；但与此同时，另一些列出的检测项却未能正确运作。工具尚有待完善，待改进部分较多，同时也产生了不佳的用户体验，对收费的企业版功能存疑。