对于 ASLR 和 PIE 一直没有系统的归纳，部分概念时常混淆，做个笔记记录一下。

ASLR 是什么？
ASLR 是 Linux操作系统的功能选项，作用于程序（ELF）装入内存运行时。是一种针对缓冲区溢出的安全保护技术，通过对加载地址的随机化，防止攻击者直接定位攻击代码位置，到达阻止溢出攻击的一种技术。

开启、关闭ASLR
查看当前系统ASLR的打开情况：

`sudo cat /proc/sys/kernel/randomize_va_space

ASLR 有三个安全等级：

0： ASLR 关闭
1：随机化栈基地址（stack）、共享库（.so\libraries）、mmap 基地址
2：在1基础上，增加随机化堆基地址（chunk）
PIE 是什么？
PIE 是 gcc 编译器的功能选项，作用于程序（ELF）编译过程中。是一个针对代码段（ .text ）、数据段（ .data ）、未初始化全局变量段（ .bss ）等固定地址的一个防护技术，如果程序开启了PIE保护的话，在每次加载程序时都变换加载地址，从而不能通过 ROPgadget 等一些工具来帮助解题。

开启 PIE
在使用 gcc 编译时加入参数-fPIE。

PIE 开启后会随机化代码段（ .text ）、初始化数据段（ .data ）、未初始化数据段（ .bss ）的加载地址。

总结
ASLR	
1：栈基地址（stack）、共享库（.so\libraries）、mmap 基地址
2：在1基础上，增加随机化堆基地址（chunk）,	属于系统功能	作用于程序（ELF）装入内存运行时
PIE	代码段（ .text ）、初始化数据段（ .data ）、未初始化数据段（ .bss ）	属于编译器功能	作用于程序（ELF）编译过程中
