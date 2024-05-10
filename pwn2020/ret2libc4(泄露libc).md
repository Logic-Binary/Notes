# ret2libc4

检查程序保护

![image-20240510170239107](图片/image-20240510170239107.png)

观察反汇编

![image-20240510170314023](图片/image-20240510170314023.png)

很明显的gets溢出，程序是动态链接，找不到system函数，也无法构造rop链，还是考虑泄露libc函数来完成攻击。

这题跟ret2libc3不同的是我需要溢出两次，第一次gets返回到main函数，再次执行puts函数拿到puts函数在libc中的实现地址，第二次gets后返回到system函数，