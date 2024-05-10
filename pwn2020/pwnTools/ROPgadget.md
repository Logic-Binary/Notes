ROPgadget使用

1.查找字符串'/bin/sh'

```shell
ROPgadget --binary file --string '/bin/sh'
```

2.查找寄存器的gadgets

```shell
ROPgadget --binary rop --only 'pop|ret' | grep 'eax'
```

