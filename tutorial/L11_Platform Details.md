# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第十篇文章。主要是来介绍平台细节。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



## 平台细节（Platform Details）



这一节包含访问键盘和表现良好所需的繁琐但是必须的细节。这些对于了解虚拟机并不具有洞察力或相关性。



**这一段代码随意复制粘贴！**



如果你试图在 non-Unix 的操作系统上运行虚拟机，比如 Windows。这些函数需要用 Windows 特定的版本被替换。



```c
uint16_t check_key()
{
    fd_set readfds;
    FD_ZERO(&readfds);
    FD_SET(STDIN_FILENO, &readfds);

    struct timeval timeout;
    timeout.tv_sec = 0;
    timeout.tv_usec = 0;
    return select(1, &readfds, NULL, NULL, &timeout) != 0;
}
```



这将从程序参数中提取程序路径，并在缺少使用示例时输出它们。

```c
if (argc < 2)
{
    /* show usage string */
    printf("lc3 [image-file1] ...\n");
    exit(2);
}

for (int j = 1; j < argc; ++j)
{
    if (!read_image(argv[j]))
    {
        printf("failed to load image: %s\n", argv[j]);
        exit(1);
    }
}
```



这是用于设置终端输入的 Unix 特定代码。



```c
struct termios original_tio;

void disable_input_buffering()
{
    tcgetattr(STDIN_FILENO, &original_tio);
    struct termios new_tio = original_tio;
    new_tio.c_lflag &= ~ICANON & ~ECHO;
    tcsetattr(STDIN_FILENO, TCSANOW, &new_tio);
}

void restore_input_buffering()
{
    tcsetattr(STDIN_FILENO, TCSANOW, &original_tio);
}
```



程序中断时，我们希望将终端设置恢复正常。



```c
void handle_interrupt(int signal)
{
    restore_input_buffering();
    printf("\n");
    exit(-2);
}
```



```c
signal(SIGINT, handle_interrupt);
disable_input_buffering();
```



```c
restore_input_buffering();
```



```c
{Sign Extend, 6}
{Swap, 10}
{Update Flags, 6}
{Read Image File, 10}
{Read Image, 10}
{Check Key, 12}
{Memory Access, 11}
{Input Buffering, 12}
{Handle Interrupt, 12}
```



```c
// includes
#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>
#include <string.h>
#include <signal.h>
#include <unistd.h>
#include <fcntl.h>

#include <sys/time.h>
#include <sys/types.h>
#include <sys/termios.h>
#include <sys/mman.h>
```





