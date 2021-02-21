# Solidity-浮点数处理


对 Solidity 中如何进行浮点数的处理做说明。

<!--more-->

首先声明，Solidity 中支持浮点数定义，但无法赋值和进行计算。文档中对其描述是「目前还不完全支持」，虽然这意味着以后可能会完全支持，但等不及了，下面记录几个可参考的资料。

1. 来自 [ethereum stackexchange](https://ethereum.stackexchange.com/questions/83785/what-fixed-or-float-point-math-libraries-are-available-in-solidity) 中的回答，介绍了一些可用的库；
2. [ABDK Math Quad](https://github.com/abdk-consulting/abdk-libraries-solidity/blob/master/ABDKMathQuad.md)，包含两个合约库，一个支持定点数，一个支持浮点数；
3. Mikhail Vladimirov 的 [Math in Solidity](https://medium.com/coinmonks/math-in-solidity-part-1-numbers-384c8377f26d) 系列文章，介绍如何在 Solidity 中处理各种数学运算，写的非常棒。
