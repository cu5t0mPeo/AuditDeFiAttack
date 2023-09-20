# 20230203-Orion Protocol

Reentrancy

交易哈希：0xa6f63fcb6bec8818864d96a5b1bb19e8bd85ee37b2cc916412e720988440b2aa、0xfb153c572e304093023b4f9694ef39135b6ed5b2515453173e81ec02df2e2104

攻击合约1：0x5061f7e6dfc1a867d945d0ec39ea2a33f772380a

攻击合约2：0x64acd987a8603eeaf1ee8e87addd512908599aec

受攻击地址：0x420a50a62b17c18b36c64478784536ba980feac8

PoolFunctionality._doSwapTokens合约函数因为存在reentrancy问题，导致资产计算错误。



攻击者在真正攻击前，做了一些准备，首先是创建了伪代币合约(ATK)，然后通过添加流动性，创建了USDC-ATK、ATK- USDT的Pair。

## 攻击流程如下

1.攻击者存入500000 USDC

2.攻击者通过uniswap v2闪电贷，贷与被攻击合约的usdt相等的金额2844766426325

3.攻击者调用Orion Protocol的swapThroughOrionPool函数，path为[USDC, ATK, USDT]，花费10000USDC(Pair都为攻击者创建)

4.合约PoolFunctionality执行doSwapThroughOrionPool时，最终会执行Pair的swap函数，而swap会执行代币的transfer。

5.执行ATK的transfer，这是攻击者伪造的代币合约，这个函数会去调用攻击合约的deposit，deposit会调用Orion Protocol的deposit。攻击者存入了当前合约等量的usdt金额。

6.此时漏洞合约的金额已改变，返回PoolFunctionality合约后，会进入一个错误的计算，这将导致攻击者在进行代币代换的时候，代换的目标代币将计算错误。

7.调用withdraw取出所有的金额。



## 合约分析

这里在调用swap前和调用后的价格是不同的，原因就在swap这里是有重入的漏洞的，swap最后调用的是pair里的swap函数，而pair的swap会调用token的transfer函数，而token是攻击者伪造的，所以合约会去调用伪造token的transfer函数，这个函数最终会调用攻击合约的deposit函数。deposit函数最终会调用Exchange.sol合约进行金额存储，此时池的金额也就是下图的toAuto金额增多，导致amountOut计算错误。

![image-20230919200023110](https://pic.gksec.com/20230919/65098d57ecdae.png)

我们在来看下deposit：

没有重入保护

![image-20230919200604097](https://pic.gksec.com/20230919/65098eac70ae9.png)

## 问题出现原因

对于某些交易所项目来说，一些跟支付挂钩的函数都应该有重入保护，否则将可能会有类似的方法出现。在这里出现问题的便是存款函数没有重入保护导致池里的所有资金被窃取。

