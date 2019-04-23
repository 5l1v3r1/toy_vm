# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第九篇文章。主要是来介绍如何加载程序 。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



我们已经提到了很多关于从内存加载和执行指令的内容，但是指令首先如何进入内存？**将汇编程序转换为机器代码时，结果是包含指令和数据的数组文件**。这个可以通过从内存中复制进行加载





程序文件的前 16 位指定程序应该从内存中开始的地址。该地址称为**原点（origin）**。必须首先读取它，然后从原始地址开始将其余数据从文件读入。



以下是将 LC-3 程序读入内存的代码：



```c
void read_image_file(FILE* file)
{
    /* the origin tells us where in memory to place the image */
    uint16_t origin;
    fread(&origin, sizeof(origin), 1, file);
    origin = swap16(origin);

    /* we know the maximum file size so we only need one fread */
    uint16_t max_read = UINT16_MAX - origin;
    uint16_t* p = memory + origin;
    size_t read = fread(p, sizeof(uint16_t), max_read, file);

    /* swap to little endian */
    while (read-- > 0)
    {
        *p = swap16(*p);
        ++p;
    }
}
```



请注意，在每个加载的值上调用 swap16。 LC-3 程序是大端程序，但我们使用的大多数现代计算机都是小端程序。因此，我们需要交换每个加载的 uint16。 （如果您碰巧使用的是奇怪的计算机，比如PPC 那么您就不应该交换。）



```c
uint16_t swap16(uint16_t x)
{
    return (x << 8) | (x >> 8);
}
```



**Note**：字节顺序（[Endianness](https://en.wikipedia.org/wiki/Endianness)）是指如何解释整数的字节数。在 little-endian 中，第一个字节是低字节数字，而在 big-endian 中，它是相反的。据我所知，这个决定大多是武断的。不同的公司做出了不同的决定，所以现在我们留下了不同的实现。您不需要知道有关此项目的字节顺序的任何其他信息。



我们还为 read_image_file 添加一个好用的函数，它将路径作为字符串;

```c
int read_image(const char* image_path)
{
    FILE* file = fopen(image_path, "rb");
    if (!file) { return 0; };
    read_image_file(file);
    fclose(file);
    return 1;
}
```

