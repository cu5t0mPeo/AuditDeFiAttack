# 20230207 - FDP - Reflection token

本篇文章属于自己的第一篇反射代币的攻击分析，但目前只能从代码角度分析，还无法从业务角度去分析，不过还是很开心的。

交易哈希：0x09925028ce5d6a54801d04ff8f39e79af6c24289e84b301ddcdb6adfa51e901b

攻击合约：0xe02970bd38b283c3079720c1e71001abe001bc83

漏洞合约：0xe02970bd38b283c3079720c1e71001abe001bc83



## 攻击流程分析

1.攻击者通过闪电贷获取1363426920555815103015 WETH

2.通过pancakeSwap将16326701609462839506 WETH换为FDP token 49935372988746381368951，该token是反射token

3.调用fdp token的deliver，该函数会根据参数tAmount使得汇率变小，变相的说使用户手中fdp增值，tAmount越大并且不是通货紧缩的合约，那么其金额就会变大。而攻击者tAmount值为28463162603585437380302，占有总fdp的28%。

4.由于fdp增值，此时调用balanceOf时会使pancakeSwap池中的fdp价值从50060579699495119166869到11122277578830245110611430

5.先调用getAmountsOut查看多出来的fdp能换多少weth

6.调用swap函数，将多出来的fdp全部换成weth，收获32506065889868331731

7.偿还闪电贷，多余的套现跑路

## 合约分析

![image-20230919224616905](https://pic.gksec.com/20230919/6509b43a7a061.png)

![image-20230919224719914](https://pic.gksec.com/20230919/6509b4784ce36.png)

![image-20230919224818077](https://pic.gksec.com/20230919/6509b4b25dde3.png)

![image-20230919224955452](https://pic.gksec.com/20230919/6509b513b6109.png)

![image-20230919225100895](https://pic.gksec.com/20230919/6509b55528839.png)

![image-20230919225125452](https://pic.gksec.com/20230919/6509b56db95e9.png)

最终我们可以知道由于汇率减小，使得balanceOf计算出来的金额变大，最终攻击者利用了这一点

## 问题出现原因

第一次分析这种token，其实对于原因还不是很熟悉，所以这下面的是临时的想法：

其实总归来说这次问题在reflection token中也是一种福利政策的计算方式较为粗糙导致的，对于这种token的福利应该用更为精妙的方式增加用户手中token的金额。所以这类福利政策计算方式粗糙的token比较容易控制。

## 参考

https://twitter.com/BeosinAlert/status/1622806011269771266