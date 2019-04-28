# 写一个属于自己的虚拟机（Write your Own Virtual Machine）



## 介绍

整个代码和文章来自[大神](https://justinmeiners.github.io/lc3-vm/)。在大神的这篇文章中，用非常通俗易懂的语言，教你如何从 0 开始写一个能运行汇编二进制文件的虚拟机。通过此次学习，我学习到：



+ 虚拟机的本质到底是什么
+ 如何用 C 实现一个能运行汇编二进制文件的虚拟机



在 `code` 文件夹中我实现了自己的 `toy_vm` 。在 `tutorial` 文件夹中翻译了文章。



![](https://github.com/TensShinet/toy_vm/blob/master/images/toy_vm.png?raw=true)





大神的教程，讲得很透彻。只要跟着文章的思路，学习大神的代码。就能写出一个能运行 2048 的虚拟机



![](https://github.com/TensShinet/toy_vm/blob/master/images/2048.png?raw=true)







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



然后我们交个朋友？



![](https://github.com/TensShinet/learn_statistical-learning-method/blob/master/images/wx.png?raw=true)



