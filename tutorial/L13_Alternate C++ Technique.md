# 写一个属于自己的虚拟机（Write your Own Virtual Machine）

> 前言：这是 Write your Own Virtual Machine 的第十三篇文章。主要是来介绍如何用 c++ 完成这个项目。原文在[这里](https://justinmeiners.github.io/lc3-vm/)



这一节将展示一个高级的办法执行指令使整个代码更小一点。这一章可跳过



由于 C++ 具有强大的泛型编译能力，我们可以使用编译器为我们生成部分指令。这种方法减少了代码重复，实际上更接近计算机在硬件中的连线方式。



这里的想法是重用每条指令共有的步骤。例如，一些指令使用间接寻址或符号扩展值并将其添加到当前 PC 值。如果我们能为所有指令编写一次这段代码，那不是很好吗？



通过将指令视为步骤流程，我们可以看到每条指令只是几个较小步骤的排列。我们将使用按位标志来标记为每条指令执行哪些步骤。

在 bit 中的 1 与指令编号有关，暗示编译器应该在那条指令中包括这一节的代码。

```c++
template <unsigned op>
void ins(uint16_t instr)
{
    uint16_t r0, r1, r2, imm5, imm_flag;
    uint16_t pc_plus_off, base_plus_off;

    uint16_t opbit = (1 << op);
    if (0x4EEE & opbit) { r0 = (instr >> 9) & 0x7; }
    if (0x12E3 & opbit) { r1 = (instr >> 6) & 0x7; }
    if (0x0022 & opbit)
    {
        r2 = instr & 0x7;
        imm_flag = (instr >> 5) & 0x1;
        imm5 = sign_extend((instr) & 0x1F, 5);
    }
    if (0x00C0 & opbit)
    {   // Base + offset
        base_plus_off = reg[r1] + sign_extend(instr & 0x3f, 6);
    }
    if (0x4C0D & opbit)
    {
        // Indirect address
        pc_plus_off = reg[R_PC] + sign_extend(instr & 0x1ff, 9);
    }
    if (0x0001 & opbit)
    {
        // BR
        uint16_t cond = (instr >> 9) & 0x7;
        if (cond & reg[R_COND]) { reg[R_PC] = pc_plus_off; }
    }
    if (0x0002 & opbit)  // ADD
    {
        if (imm_flag)
        {
            reg[r0] = reg[r1] + imm5;
        }
        else
        {
            reg[r0] = reg[r1] + reg[r2];
        }
    }
    if (0x0020 & opbit)  // AND
    {
        if (imm_flag)
        {
            reg[r0] = reg[r1] & imm5;
        }
        else
        {
            reg[r0] = reg[r1] & reg[r2];
        }
    }
    if (0x0200 & opbit) { reg[r0] = ~reg[r1]; } // NOT
    if (0x1000 & opbit) { reg[R_PC] = reg[r1]; } // JMP
    if (0x0010 & opbit)  // JSR
    {
        uint16_t long_flag = (instr >> 11) & 1;
        pc_plus_off = reg[R_PC] +  sign_extend(instr & 0x7ff, 11);
        reg[R_R7] = reg[R_PC];
        if (long_flag)
        {
            reg[R_PC] = pc_plus_off;
        }
        else
        {
            reg[R_PC] = reg[r1];
        }
    }

    if (0x0004 & opbit) { reg[r0] = mem_read(pc_plus_off); } // LD
    if (0x0400 & opbit) { reg[r0] = mem_read(mem_read(pc_plus_off)); } // LDI
    if (0x0040 & opbit) { reg[r0] = mem_read(base_plus_off); }  // LDR
    if (0x4000 & opbit) { reg[r0] = pc_plus_off; } // LEA
    if (0x0008 & opbit) { mem_write(pc_plus_off, reg[r0]); } // ST
    if (0x0800 & opbit) { mem_write(mem_read(pc_plus_off), reg[r0]); } // STI
    if (0x0080 & opbit) { mem_write(base_plus_off, reg[r0]); } // STR
    if (0x8000 & opbit)  // TRAP
    {
         {TRAP, 8}
    }
    //if (0x0100 & opbit) { } // RTI
    if (0x4666 & opbit) { update_flags(r0); }
}
```



```c++
static void (*op_table[16])(uint16_t) = {
    ins<0>, ins<1>, ins<2>, ins<3>,
    ins<4>, ins<5>, ins<6>, ins<7>,
    NULL, ins<9>, ins<10>, ins<11>,
    ins<12>, NULL, ins<14>, ins<15>
};
```



**Note**:我从 [Bisqwit的NES](https://www.youtube.com/watch?v=QIUVSD3yqqE) 中学到了这种技术。如果您对仿真或NES感兴趣，我强烈推荐他的视频。



C++ 版本的其余部分使用我们已编写的代码。 完整代码在[这里](https://justinmeiners.github.io/lc3-vm/src/lc3-alt.cpp)



```c
{Includes, 12}

{Registers, 3}
{Condition Flags, 3}
{Opcodes, 3}

{Memory Mapped Registers, 11}
{TRAP Codes, 8}

{Memory Storage, 3}
{Register Storage, 3}

{Functions, 12}

int running = 1;
{Instruction C++, 14}
{Op Table, 14}

int main(int argc, const char* argv[])
{
    {Load Arguments, 12}
    {Setup, 12}

    enum { PC_START = 0x3000 };
    reg[R_PC] = PC_START;

    while (running)
    {
        uint16_t instr = mem_read(reg[R_PC]++);
        uint16_t op = instr >> 12;
        op_table[op](instr);
    }
    {Shutdown, 12}
}
```