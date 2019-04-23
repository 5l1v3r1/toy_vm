# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第七篇文章。主要是来介绍陷入例程 。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



## 陷入例程 （Trap Routines）

LC-3 提供了一些预定义的例程，用于执行常见任务和与 I/O 设备交互。例如，有一些例程用于从键盘获取输入以及向控制台显示字符串。这些被称为陷入例程，你可以将其视为 LC-3 的操作系统或 API。为每个陷入例程分配一个识别它的陷入代码（类似于操作码）。执行一个的陷入例程时候，**Trap** 指令就会被调用以及所需例程的代码。



![Trap Encoding](https://justinmeiners.github.io/lc3-vm/img/trap_layout.gif)



定义每一个 trap code



```c
enum
{
    TRAP_GETC = 0x20,  /* get character from keyboard, not echoed onto the terminal */
    TRAP_OUT = 0x21,   /* output a character */
    TRAP_PUTS = 0x22,  /* output a word string */
    TRAP_IN = 0x23,    /* get character from keyboard, echoed onto the terminal */
    TRAP_PUTSP = 0x24, /* output a byte string */
    TRAP_HALT = 0x25   /* halt the program */
};
```



你可能想知道为什么陷入例程的代码不包含在说明中。这是因为它们实际上并没有向 LC-3 引入任何新功能，它们只是提供了一种执行任务的便捷方式（类似于 C 中的系统功能）。在官方的 LC-3 模拟器中，陷入例程是用汇编语言编写的。调用陷入例程代码时，PC 将移动到该代码的地址。 CPU 执行该过程的指令，并在完成后将 PC 重置为调用陷入历程代码的位置。



**Note**: 这就是程序从地址 0x3000 而不是 0x0 开始的原因。较低的地址留空，为陷入例程代码留出空间。



没有关于必须如何实现陷阱例程的规范，只有他们应该做的事情。在我们的 VM 中，我们将通过在 C 中编写它们。当调用陷阱例程代码时，将调用 C 函数。完成后，将返回说明。



尽管陷入例程可以用汇编语言编写，这就是物理 LC-3 计算机的功能，但它并不适合虚拟机。我们可以利用操作系统上可用的优势，而不是编写我们自己的原始 I/O 例程。这将使 VM 在我们的计算机上运行得更好，简化代码，并提供更高级别的可移植性抽象。



**Note**：从键盘获取输入就是一个具体的例子。汇编版本使用循环连续检查键盘输入。这消耗了大量的 CPU 时间！使用适当的 OS 输入功能，允许程序休眠直到收到输入。



在 switch case 里面添加 TRAP code，添加另一个 switch：

```c
{
    case TRAP_GETC:
        {TRAP GETC, 9}
        break;
    case TRAP_OUT:
        {TRAP OUT, 9}
        break;
    case TRAP_PUTS:
        {TRAP PUTS, 8}
        break;
    case TRAP_IN:
        {TRAP IN, 9}
        break;
    case TRAP_PUTSP:
        {TRAP PUTSP, 9}
        break;
    case TRAP_HALT:
        {TRAP HALT, 9}
        break;
}
```



与说明书一样，我将向你展示如何实现单个陷入例程并将其余部分留给你。



### PUTS

PUTS 陷入例程代码用于输出以 null 结尾的字符串（类似于 C 中的 printf）。见说明书第 543 页。 要显示字符串，我们必须为陷入例程提供要显示的字符串。这是通过在开始调用之前将第一个字符的地址存储在 R0 中来完成的。



说明书中说：



> 将一串 ASCII 字符写入控制台显示。字符包含在连续的内存单元中，一个字符占一个内存单元，从 R0 中指定的地址开始，以 x0000 的出现终止。 （第543页）（Write a string of ASCII characters to the console display. The characters are contained in consecutive memory locations, one character per memory location, starting with the address specified in `R0`. Writing terminates with the occurrence of `x0000` in a memory location. (Pg. 543)）



**Note**：请注意，与 C 字符串不同，字符不存储在单个字节中，而是存储在单个内存单元中。 LC-3 的内存单元是 16 位，因此字符串中的每个字符都是 16 位宽。要使用 C 函数显示它，我们需要将每个值转换为char 并单独输出它们。



```c
{
    /* one char per word */
    uint16_t* c = memory + reg[R_R0];
    while (*c)
    {
        putc((char)*c, stdout);
        ++c;
    }
    fflush(stdout);
}
```



这就是 PUTS 陷入例程。如果你熟悉 C，那么陷入例程非常简单。回到说明书中并立即实现其他陷入例程。完整的代码可以在本教程的最后找到。