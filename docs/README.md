# 区块链安全服务平台调研测试报告

- **李儆卿**

### 免责声明

本次调研行为基于个人兴趣爱好进行，非商业评测，不涉及任何商业目的或行为，与其它任何公司、组织及个人均无关联；此文给出的评价结论均为个人使用主观意见，不反映平台真实能力水平，对使用平台造成的任何错误、危害不承担任何责任；文中引用的软件、工具、图片、文本内容均来自合法公开网站授权使用并展示部分，本人对其准确性、内容、完整性、合法性、可靠性、可用性不承担任何责任；测试合约代码均为本人编写，无任何隐私信息披露情况；未经同意不得转载、引用此文，本人对使用/传播此文造成的任何直接、间接伤害损失不承担任何责任；如认为此文有侵犯权益情况，可联系本人移除，本人不为此承担任何法律责任。



## 前提说明： 

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

---





## 1.调研测试结论概要：

注：本节用于快速获取调研结论，节省时间。如需关注更多技术实现细节，请阅读2-5节部分

### 产品定位

​       <font color = d2b48c>安全服务平台针对担心源代码泄漏的用户，推出离线插件版工具；又为持观望态度的潜在用户，提供快捷的在线版试用；在此基础上，通过提供功能扩展的企业版，以及人工审计服务，来进一步细分客户群体。</font>

​       个人观点：产品定位清晰，目标明确，值得借鉴。

### 试用结论

​      <font color = d2b48c>1. 工具目前仅适用于以太坊solidity智能合约的安全扫描检测。</font>

​       个人观点：由于限定了区块链类型和合约语言，不具备泛用性。当使用非solidity语言开发智能合约时，其参考价值有限。

​       <font color = d2b48c>2. 实际使用过程中，发现离线插件版和在线运行版工具扫描结果存有不一致情况。</font>

​        个人观点：由于两者核心部分相同，且均存在一方探知漏洞而对方却无法检出的情况。因此，暂认为这种结果差异是工具自身问题，而非市场行为所致。

​       <font color = d2b48c>单就离线版而言，通过编写带漏洞的solidity智能合约提交检测，发现一部分声称已覆盖的检测项未能正确运作，使得合约中存在的某些漏洞未被发现。</font>

​        个人观点：给个人带来了不佳的用户体验，暂认为工具尚有不少有待改进之处，同时也导致对收费的企业版完全功能存疑。

​       <font color = d2b48c>3. 然而，他山之石可以攻玉。 安全服务平台归纳出的具体检测项种类，深入语言规范的思路，以及从以太坊实际黑客行为中总结的方法，很有学习意义。</font>

​       个人观点：当检测其它语言开发的智能合约时，也需对其特性和限制进行挖掘，结合区块链平台具体事例，才能开发出更完善、更具泛用性的安全工具。

---





## 2. 支持的智能合约语言类型

根据产品说明，该平台当前只针对以太坊(Ethereum，以下可简称ETH)智能合约进行安全检测和审计报告。

测试中发现**离线版和在线版两种工具核心并无区别**，针对智能合约代码进行代码规范、语法错误、安全特性的扫描检测。



以太坊当前主要支持的智能合约语言为Solidity和Vyper，来自官方说明：

[Smart Contract Languages](https://ethereum.org/developers/#smart-contract-languages)

*Any program that runs on the Ethereum Virtual Machine (EVM) is commonly referred to as a “smart contract”. The most popular languages for writing smart contracts on Ethereum are **Solidity** and **Vyper**, though there are [others under development](https://github.com/ConsenSys/ethereum-developer-tools-list#smart-contract-languages).*



实际测试中发现平台仅支持Solidity语言的智能合约安全检测

![](.\Image\online_checking_1.png)



尝试进行了Vyper智能合约的检测，检测工具提示编译失败，视其为不合法代码。

![](.\Image\online_checking_2.png)



### 结论：

此安全检测工具受限于区块链类型（以太坊）及合约类型（Solidity），在泛用性方面参考价值有限。

---





## 3.支持的安全检测内容

参见免费版、企业版和人工审计检测内容比较图：

![](.\Image\compareversion.png)

---





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

tx Origin是solidity的一个状态变量，针对其可进行网络钓鱼攻击，耗尽所有资金。
特点为: 受害者在智能合约中使用tx.origin检查合同所有者的身份.
```



<font color = daa520 size = 4 face="calibri">Invoke low level Calls </font>

```Text
Invoke low level Calls:

call是Solidity提供的底层函数，用来与外部合约或者库进行交互。
在使用时，必须对调用参数的安全性进行判定，否则易造成合约漏洞。
```



<font color = daa520 size = 4 face="calibri">Unchecked Call or send Return values </font>

```Text
Unchecked Call or send Return values:

在调用call/send函数后, 失败也不会直接抛出异常。
如果不检查调用返回值，函数会继续执行，造成合约漏洞。
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

合约代码中严格限制了合约的资金，而合约资金是可变的。因此，能利用这个特性，
使得合约的功能逻辑无法正常执行。比如，函数具有条件声明，执行取决于该合同的余额低于一定金额，
则可能会绕过该声明。
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



### 案例4

#### 覆盖内容：

|     类型     |                      检测项                      |
| :----------: | :----------------------------------------------: |
| 代码规范检测 | <font color = daa520>Construtor Mistyping</font> |

#### 说明：

<font color = daa520 size = 4 face="calibri">Constructor Mistyping </font>

```javascript
Construtor Mistyping:

在Solidity 0.4.22版本之前，构造函数可以用与合约名称相同的函数定义。
这样，如果合约名称被修改，而忽略了构造函数名称的修改，会导致漏洞。

因此，从Solidity 0.4.22版本开始，用关键字 constructor 来指明构造函数
```

智能合约漏洞代码：

创建Solidity 0.4.23版本的智能合约：

```javascript
pragma solidity 0.4.23;

contract ConstructLeak {
    string public str;

    // 构造函数
    construtor (string _str) public {
        str = _str;
    }
}
```

工具扫描后报告正常：

```Text
PID: 5736

start compile!
compile over!
start deploy!
deploy over!
No problem was found in the contract
---end---
```

将合约中的构造函数改为与合约同名，再次扫描无漏洞检出：

```javascript
pragma solidity 0.4.23;

contract ConstructLeak {
    string public str;

    // 构造函数
    function ConstructLeak (string _str) public {
        str = _str;
    }
}
```

更改Solidity版本，由于插件必须对应匹配版本，亦无法检出漏洞。但此时Solidity高版本的编译器实际会警告<font color = daa520 size = 3 face="calibri">构造函数与合约重名的方法已废弃，建议使用</font> <font color = daa520 size = 4 face="calibri">Constructor</font> <font color = daa520 size = 3 face="calibri">关键字</font>。

```javascript
pragma solidity 0.4.18;

contract ConstructLeak {
    string public str;

    // 构造函数
    function ConstructLeak (string _str) public {
        str = _str;
    }
}
```

因此，从逻辑层面来看，暂认为针对<font color = daa520 size = 4 face="calibri"> Constructor Mistyping </font>代码漏洞，工具检测失败。



### 整体结论：

自行编写带漏洞的solidity智能合约，将其提交至安全检测工具进行扫描检测。一部分平台声称覆盖的合约漏洞能够被发现；但与此同时，另一些列出的检测项却未能正确运作。工具尚有待完善，待改进部分较多，同时也产生了不佳的用户体验，对收费的企业版功能存疑。



## 5.附录

注：本节为区块链安全服务平台所有检测项内容的具体说明。以下将用<font color = 8fbc8f>"绿色"</font>标出具备一定通用性可能，可作为参考借鉴，用于检测不同语言开发的智能合约的部分。

### 1.代码规范检测

主要针对合约编写时的一些代码规范进行检测，共有11小项。

- 1.1 `ERC规范`

  包含了以太坊C20，ERC721，ERC1400，ERC1404，ERC223，ERC777等常见的合约标准检测，确保了开发人员能正确实现这些标准。

- <font color = 8fbc8f>1.2 `Transfer To Zero Address`</font>

  <font color = 8fbc8f>在transfer、transferFrom、transferOwnership等敏感函数中，用户操作不可逆，所以建议开发者在这些函数实现中增加目标地址非零检查，避免用户误操作导致用户权限丢失和财产损失</font>

- <font color = 8fbc8f>1.3 `TX Origin Authentication`</font>

  <font color = 8fbc8f>tx.origin是Solidity的一个状态变量，它遍历整个调用栈并返回最初发送调用（或事务）的帐户的地址。在智能合约中使用此变量进行身份验证会使合约容易受到类似网络钓鱼的攻击。</font>

- <font color = 8fbc8f>1.4 `Constructor Mistyping`</font>

  <font color = 8fbc8f>构造函数仅在合约部署的时候被调用，合约owner的设置一般放在构造函数中，合约的构造函数还会执行初始化的操作。在使用function的方式定义构造函数时，如果函数名与合约名失配，就变成了一个普通函数。那么，合约将存在重大安全风险。</font>

- 1.5 `Complex Code In Fallback Function`

  合约的fallback函数通常用以接收一笔转账，但如果在fallback里实现过于复杂的逻辑，可能会将gas耗尽，导致转账不成功。

- 1.6 `Unary Operation`

  当定义的操作的意图是将数字与变量+=相加但却意外地以错误的方式定义=+时，会出现错误。它不是计算总和，而是再次初始化变量。

- <font color = 8fbc8f>1.7 `Redefine Variable From Base Contracts`</font>

  <font color = 8fbc8f>Solidity中同一合约或不同合约允许有相同的状态变量，他们不会构成直接威胁，在单个相当于重新定义了这个变量，在多个合约中继承使用时会出现先后关系和使用错误的情况，所以尽量避免出现相同的状态变量。</font>

- <font color = 8fbc8f>1.8 `Unused Variables`</font>

  <font color = 8fbc8f>Solidity中允许有未使用的变量，它们不会构成直接的安全问题，但会降低代码的可读性并且额外占用存储空间导致部署时的资源消耗增加。</font>

- <font color = 8fbc8f>1.9 `No Return`</font>

  <font color = 8fbc8f>如果声明一个函数有返回值，而最后没给它返回值，就会产生一个默认的返回值，而默认返回值和实际执行后的返回值可能存在差异。</font>

- <font color = 8fbc8f>1.10 `Overload Syscall`</font>

  <font color = 8fbc8f>对于Solidity已内置函数如assert，如果在合约中进行了重定义，可能会出现异常。</font>

- 1.11 `Fake Recharge Vulnerability`

  ERC20合约在transfer函数中可能失败，这时函数并没有异常退出，而是return false; 如果交易所根据调用状态来判断转账是否成功，将会导致错误的判断。

### 2.函数调用检测

检查合约中在进行函数调用时可能出现的问题，共有5小项。

- 2.1 `Invoke Low Level Calls`

  call是以太坊智能合约编写语言Solidity提供的底层函数，用来与外部合约或者库进行交互。此类函数使用时需要对调用参数的安全性进行判定；delegatecall会保持调用环境不变的属性表明，构建无漏洞的定制库并不像人们想象的那么容易。库中的代码本身可以是安全的，无漏洞的，但是当在另一个应用的环境中运行时，可能会出现新的漏洞；selfdestruct自杀函数的调用会销毁合约。

- 2.2 `Invoke Extcodesize`

  extcodesize在合约部署的时候为零，攻击者可以在自己的构造函数中调用受害合约，这个时候使用extcodesize验证是无效的。

- 2.3 `Invoke Ecrecover`

  keccak256()和ecrecover()都是内嵌的函数，keccak256()可以用于计算公钥的签名，ecrecover()可以用来恢复签名公钥。传值正确的情况下，可以利用这两个函数来验证地址。但当ecrecover()的参数错误时候，返回0x0地址，如果_from也传入0x0地址，就能通过校验。也就是说，任何人都可以将0x0地址的余额转出

- 2.4 `Unchecked Call Or Send Return Values`

  在调用call`/`send函数后无论执行成功还是失败都不会直接抛异常，如果不对调用返回值进行检查，函数会继续执行。

- 2.5 `Re Entrancy`（企业版）

  合约内错误的逻辑实现可能导致重入调用，用户能重复转走Token。

### 3.业务逻辑安全检测

检查可能导致业务逻辑出现安全风险的问题，共有6个检查项。

- <font color = 8fbc8f>3.1 `Block Members Manipulation`</font>

  <font color = 8fbc8f>区块参数依赖风险主要有时间戳依赖和区块哈希依赖，这种风险主要来自于使用他们生成随机数，因为它们可以被操纵或者被攻击者获取，所以不应该用于随机种子。</font>

- 3.2 `Arbitrary Jump with Function Type Variable`

  由于`Solidity`不支持指针算术，因此无法将此变量更改为任意值。但是，如果开发人员在最坏的情况下使用汇编指令（例如或赋值运算符），则攻击者可以将函数类型变量指向任何代码指令，从而违反所需的验证和所需的状态更改。

- 3.3 `Check This Balance`

  合约代码中严格限制了合约的资金，而大多数情况下合约资金都是可变的，因此用户能轻松利用这个特性使得合约的功能逻辑无法正常执行。

- <font color = 8fbc8f>3.4 `Function Problem`（企业版）</font>

  <font color = 8fbc8f>函数永远只会以`revert()`等异常状态结束，无法正常执行完后return，说明函数设计出现了问题。</font>

- 3.5 `Call Problem`（企业版）

  Call调用永远失败，说明函数设计出现了问题。

- 3.6 `Denial Of Service`（企业版）

  合约可能在恶意调用之后，造成合约拒绝服务，其他用户无法正常调用合约。

### 4.溢出检测

溢出是典型的合约漏洞，可能导致检查被绕过，合约运行逻辑出错。

- <font color = 8fbc8f>4.1 `Exponent Arithmetic Overflow`</font>
- <font color = 8fbc8f>4.2 `Integer Overflow`</font>
- <font color = 8fbc8f>4.3 `Integer Underflow`</font>

### 5.异常可达状态检测

检测合约在执行过程中可能出现的异常状态，共有2个检查项。

- <font color = 8fbc8f>5.1 `Assert Fail`（企业版）</font>

  <font color = 8fbc8f>assert的限制条件是必须满足的，在条件可能不满足的情况下会报错，说明合约运行状态异常。</font>

- <font color = 8fbc8f>5.2 `Require Fail`（企业版）</font>

  <font color = 8fbc8f>与assert类似，默认require条件是可能满足的，当条件在任何情况下都无法满足会报错，说明合约运行状态异常</font>