# 20230117-OmniEstate

No Input Parameter Check

omniestategroup杯黑客攻击，损失236BNB。

攻击者交易哈希：0x49bed801b9a9432728b1939951acaa8f2e874453d39c7d881a62c2c157aa7613、0xa916674fb8203fac6d78f5f9afc604be468a514aa61ea36c6d6ef26ecfbd0e97

攻击者合约：0x1bbed50a248cfd2ce498e2219c92f86ad657e7ac

攻击者地址：0xc578d755cd56255d3ff6e92e1b6371ba945e3984

被攻击合约：0xca49ecf7e7bb9bbc9d1d295384663f6ba5c0e366



这里可以自行查看相关代码，因为这里其实是一个状态变量没有初始化的问题。

攻击流程：

1.攻击者调用invest存入1 wei

2.攻击者调用withdrawAndClaim，对存入的金额领取代币奖励，其奖励的函数是check_reward，而在这里withdrawAndClaim的第一个参数只要不是3、6、12、24就可以绕过check_reward的检查，所以状态变量不会覆盖，这样奖励金额依旧total_percent与上次相等。

## 问题出现原因

对于某个状态变量来说，如果这个状态变量涉及金额传输，但是其不会初始化，就有导致其复用上一次的结果，造成类似的错误。所以需要对状态变量赋值。

## 参考

https://twitter.com/BlockSecTeam/status/1615232012834705408