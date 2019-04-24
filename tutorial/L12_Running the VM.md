# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第十二篇文章。主要是来介绍内存映射寄存器。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



您现在可以构建并运行LC-3 VM！



+ 用你最喜欢的 C 编译器编译你的[程序](https://justinmeiners.github.io/lc3-vm/src/lc3.c)

+ 下载 [2048](https://justinmeiners.github.io/lc3-vm/supplies/2048.obj) 和 [Rogue](https://justinmeiners.github.io/lc3-vm/supplies/rogue.obj) 的汇编版本
+ 使用 obj 文件作为参数运行程序：（lc3-vm path/to/2048.obj）
+ 开始玩 2048 吧！



```c
Control the game using WASD keys.
Are you on an ANSI terminal (y/n)? y
+--------------------------+
|                          |
|                          |
|                          |
|                     2    |
|                          |
|   2                      |
|                          |
|                          |
|                          |
+--------------------------+
```



### 调试



如果你的程序不对，很有可能是因为你实现指令的那一部分有问题。这可能是棘手的调试。我建议你去阅读一个 LC-3 汇编程序的代码，同时，使用一个调试器一步一步调试虚拟机的指令代码。当你在阅读汇编的时候，确保 VM 按你期望那样运行指令。如果有差异发生，然后，您将知道哪个指令导致了问题。重新阅读其说明书并仔细检查您的代码。

