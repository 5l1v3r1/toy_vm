# 写一个属于自己的虚拟机（Write your Own Virtual Machine）



前言：这是 Write your Own Virtual Machine 的第八篇文章。主要是来给出所有陷入例程的代码 。原文在[这里](https://justinmeiners.github.io/lc3-vm/)。





##  陷入例程代码对照表（Trap Routine Cheat Sheet）



本节包含剩余陷入例程的完整代码实现。



+ 输入单个字符（Input Character）

```c
/* read a single ASCII char */
reg[R_R0] = (uint16_t)getchar();
```

+ 输出单个字符（Output Character）

```c
putc((char)reg[R_R0], stdout);
fflush(stdout);
```



+ 提示单个输入字符（Prompt for Input Character）

```c
printf("Enter a character: ");
char c = getchar();
putc(c, stdout);
reg[R_R0] = (uint16_t)c;
```



+ 输出字符串（Output String）

```c
{
    /* one char per byte (two bytes per word)
       here we need to swap back to
       big endian format */
    uint16_t* c = memory + reg[R_R0];
    while (*c)
    {
        char char1 = (*c) & 0xFF;
        putc(char1, stdout);
        char char2 = (*c) >> 8;
        if (char2) putc(char2, stdout);
        ++c;
    }
    fflush(stdout);
}
```



+ 停止程序（Halt Program）

```c
puts("HALT");
fflush(stdout);
running = 0;
```



