# 写一个属于自己的虚拟机（Write your Own Virtual Machine）



## 介绍

整个代码和文章来自[大神](https://justinmeiners.github.io/lc3-vm/)。我边翻译边学习，并把整个文章拆成了 14   小节，方便后来者学习与查看。



大神的教程，讲得很透彻。只要跟着文章的思路，就能写出一个能运行 2048 汇编游戏的虚拟机



在 `code` 文件夹中我实现了自己的 `toy_vm` 。除了实现指令之外，还要进行调试，非常花时间，各位加油 :(



最后英文水平有限，我尽量翻译，大家不要嫌弃，有错误帮忙指出来。谢谢！！！



## 目录

1. 介绍（Introduction）
2. LC-3 架构 （LC-3 Architecture）
3. 汇编举例（Assembly Examples）
4. 执行程序（Executing Programs）
5. 实现指令（Implementing Instructions）
6. 实现所有的指令（Instruction Cheat Sheet）
7. 陷入例程（Trap Routines）
8. 实现所有的陷入例程（Trap Routine Cheat Sheet）
9. 加载程序（Loading Programs）
10. 内存映射寄存器（Memory Mapped Registers）
11. 平台细节（Platform Details）
12. 运行虚拟机（Running the VM）
13. 备用的 c++ 技术（Alternate C++ Technique）
14. 相关项目（Related Projects）



## 最后

如果觉得不错，点个 star 吧。

![](https://upload-images.jianshu.io/upload_images/15548795-e9bc9fb441525b5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





