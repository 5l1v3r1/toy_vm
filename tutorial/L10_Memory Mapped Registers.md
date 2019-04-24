# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第十篇文章。主要是来介绍内存映射寄存器。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



## 内存映射寄存器（Memory Mapped Registers）



无法从普通寄存器表访问某些特殊寄存器。相反，在内存中为它们保留了一个特殊地址。要读取和写入这些寄存器，只需读取和写入其内存单元即可。这些称为**内存映射寄存器**。它们通常用于与特殊硬件设备交互。



LC-3 有两个需要实现的内存映射寄存器。它们是键盘状态寄存器（KBSR）和键盘数据寄存器（KBDR）。 KBSR 指示是否按下了某个键，KBDR 识别按下了哪个键。



虽然您可以使用 GETC 请求键盘输入，但这会阻止执行直到收到输入。 KBSR 和 KBDR 允许您轮询设备的状态并继续执行，因此程序可以在等待输入时保持响应。



```c
enum
{
    MR_KBSR = 0xFE00, /* keyboard status */
    MR_KBDR = 0xFE02  /* keyboard data */
};
```





内存映射寄存器访问内存的时候更复杂一点，我们不能直接读和写内存数组而是调用 setter 和 getter 函数。当从 KBSR 读入内存地址，getter 将要检查和更新内存地址。



```c
void mem_write(uint16_t address, uint16_t val)
{
    memory[address] = val;
}

uint16_t mem_read(uint16_t address)
{
    if (address == MR_KBSR)
    {
        if (check_key())
        {
            memory[MR_KBSR] = (1 << 15);
            memory[MR_KBDR] = getchar();
        }
        else
        {
            memory[MR_KBSR] = 0;
        }
    }
    return memory[address];
}
```



这就完成了 VM 的最后一个组件！如果您实现了其余的陷入例程和指令，那么您几乎已经准备好尝试了！



我们编写的所有内容都应按以下顺序添加到 C 文件中：

```c
{Memory Mapped Registers, 11}
{TRAP Codes, 8}

{Memory Storage, 3}
{Register Storage, 3}

{Functions, 12}

{Main Loop, 5}
```