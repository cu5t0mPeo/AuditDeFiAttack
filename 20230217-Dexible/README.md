# 20230217-Dexible

由于call函数可以任何人调用

交易哈希：0x138daa4cbeaa3db42eefcec26e234fc2c89a4aa17d6b1870fc460b2856fd11a6

攻击者合约地址：0x194fc30f9eeba9ad673413629b47fc00e71d90df

漏洞地址：0x33e690aea97e4ef25f0d140f1bf044d663091daf

## 攻击流程分析

Dexible由于fill函数里面的call函数可以任何人调用，攻击者找到一个对该合约调用了approve的用户，将用户的资金通过call函数调用transferFrom全部转走。