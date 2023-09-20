# 20230112-RoeFinance

FlashLoan price manipulation

攻击者交易哈希：0x927b784148b60d5233e57287671cdf67d38e3e69e5b6d0ecacc7c1aeaa98985b

攻击者合约：0x3a5b7db0be9f74324370fbd65b75850a5c82d176

攻击者钱包：0x67a909f2953fb1138bea4b60894b51291d2d0795

被攻击合约：0x574ff39184dee9e46f6c3229b95e0e0938e398d0



RoeFinance受到攻击者，被攻击池(0x574f)被耗尽，总损失8万美元，这次攻击的主要原因是矿池的流动性有限，导致价格预言机杯操控。

![image-20230916000520026](https://pic.gksec.com/20230916/650480c0d37fd.png)

攻击流程如下：

1.攻击者使用闪电贷获取5673的usdc，通过LendingPool.deposit存储闪电贷获取的所有usdc。

2.攻击者通过函数borrow获取uni-v2代币2953841283，债务给0x67。

3.攻击者通过deposit存储2953841283到目标池中。

4.循环步骤3、4，循环49次。

5.向目标池发送2953841283，然后执行burn，攻击者合约获取了2.9WBTC和51661USDC。

6.攻击者继续向UNI-V2传输USDC

7.调用sync控制oracle价格，此时uni-v2的价格上涨，从3495450576387056244740到4320806762049972060102

8.调用borrow借出等于闪电贷的usdc，利用这笔钱归还闪电贷，其余多余的钱全部兑换为USDC。



## 问题出现原因

Roe Finance的价格使用的是uniswap v2池，该池的流动性不够大，导致攻击者可以使用闪电贷借入大量资金，来影响池里的价格。建议DeFi的项目价格应该参考多个第三方的，并且选用的池最好要流动性较大的。

## 参考

https://twitter.com/BlockSecTeam/status/1613267000913960976