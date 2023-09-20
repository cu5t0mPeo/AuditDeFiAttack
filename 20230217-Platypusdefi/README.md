# 20230217-Platypusdefi

交易哈希：0x1266a937c2ccd970e5d7929021eed3ec593a95c68a99b4920c2efa226679b430

攻击合约：0x67afdd6489d40a01dae65f709367e1b1d18a5322

漏洞合约：0xc007f27b757a782c833c568f5851ae1dfe0e6ec7



## 攻击流程分析

1.攻击者通过闪电贷获取了44022000000000 $usdc

2.攻击者在platypus finance调用deposit获取流动性代币$LP- USDC 44000100592104

3.攻击者调用MasterPlatypusV4的deposit，为借贷做准备

4.攻击者调用borrow函数，借走 41794533641783253909672000 USP token，接着在调用合MasterPlatypusV4合约的emergencyWithdraw触发漏洞，将质押的lpusdc全部取出

5.攻击者再调用Platypus Finance的withdraw取出之前存储的usdc

6.将usp全部换为其他代币，最后再将闪电贷偿还。



## 合约分析

![image-20230920162454958](https://pic.gksec.com/20230920/650aac57e8c8c.png)

![image-20230920162834603](https://pic.gksec.com/20230920/650aad32e8695.png)

## 出现问题原因

这次问题出现在紧急取钱的函数中少了应该有的判断造成的，个人认为对于一些关键业务的函数，存钱取钱或者激励政策等这一些重要的函数都是非常容易出问题的。为此对于一些重要的业务功能，应该多考虑使用环境，以此来推测该函数在这些限制条件下是否合适，是否真的安全。