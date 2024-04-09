The PLT and GOT are sections within an ELF file that deal with a large portion of the **dynamic linking**. Dynamically linked binaries are more common than statically linked binary in CTFs. The purpose of **dynamic linking** is that binaries do not have to carry all the code necessary to run within them - this reduces their size substantially. Instead, they rely on system libraries (especially `libc`, the C standard library) to provide the bulk of the fucntionality. For example, each ELF file will not carry their own version of `puts` compiled within it - it will instead dynamically link to the `puts` of the system it is on. As well as smaller binary sizes, this also means the user can continually upgrade their libraries, instead of having to redownload all the binaries every time a new version comes out.  
PLT 和 GOT 是 ELF 文件中处理大部分动态链接的部分。在 CTF 中，动态链接的二进制文件比静态链接的二进制文件更常见。动态链接的目的是二进制文件不必携带在其中运行所需的所有代码 - 这大大减少了它们的大小。相反，它们依赖系统库（特别是 `libc` ，C 标准库）来提供大部分功能。 例如，每个 ELF 文件不会携带在其中编译的自己版本的 `puts` - 它会动态链接到其所在系统的 `puts` 。除了较小的二进制文件大小之外，这还意味着用户可以不断升级其库，而不必在每次新版本出现时重新下载所有二进制文件。


So when it's on a new system, it replaces function calls with hardcoded addresses?  
那么当它在新系统上时，它会用硬编码地址替换函数调用吗？

Not quite. 不完全的。

The problem with this approach is it requires `libc` to have a constant base address, i.e. be loaded in the same area of memory every time it's run, but remember that [_**ASLR**_](https://en.wikipedia.org/wiki/Address_space_layout_randomization) exists. Hence the need for _dynamic_ linking. Due to the way ASLR works, these addresses need to be resolved _every time the binary is run_. Enter the PLT and GOT.  
这种方法的问题是它需要 `libc` 有一个恒定的基地址，即每次运行时都加载到同一内存区域，但请记住 ASLR 存在。因此需要动态链接。由于 ASLR 的工作方式，每次运行二进制文件时都需要解析这些地址。输入 PLT 和 GOT。


## The PLT and GOT 

The PLT (**Procedure Linkage Table**) and GOT (**Global Offset Table**) work together to perform the linking.  
PLT（过程链接表）和GOT（全局偏移表）一起工作来执行链接。

When you call `puts()` in C and compile it as an ELF executable, it is not _actually_ `puts()` - instead, it gets compiled as `puts@plt`. Check it out in GDB:  
当您在 C 中调用 `puts()` 并将其编译为 ELF 可执行文件时，它实际上并不是 `puts()` - 相反，它被编译为 `puts@plt` 。在GDB中查看：

![[Pasted image 20240408103641.png]]
Why does it do that?  
为什么要这样做？

Well, as we said, it doesn't know where `puts` actually is - so it jumps to the PLT entry of `puts` instead. From here, `puts@plt` does some very specific things:  
好吧，正如我们所说，它不知道 `puts` 实际上在哪里 - 因此它会跳转到 `puts` 的 PLT 条目。从这里开始， `puts@plt` 做了一些非常具体的事情：

- If there is a GOT entry for `puts`, it jumps to the address stored there.  
    如果有 `puts` 的 GOT 条目，它将跳转到存储在那里的地址。
    
- If there isn't a GOT entry, it will resolve it and jump there.  
    如果没有 GOT 条目，它将解析它并跳转到那里。
    

The GOT is a _massive_ table of addresses; these addresses are the actual locations in memory of the `libc` functions. `puts@got`, for example, will contain the address of `puts` in memory. When the PLT gets called, it reads the GOT address and redirects execution there. If the address is empty, it coordinates with the `ld.so` (also called the **dynamic linker/loader**) to get the function address and stores it in the GOT.  
GOT 是一个庞大的地址表；这些地址是 `libc` 函数在内存中的实际位置。例如， `puts@got` 将包含内存中 `puts` 的地址。当 PLT 被调用时，它读取 GOT 地址并将执行重定向到那里。如果地址为空，则与 `ld.so` （也称为动态链接器/加载器）协调获取函数地址并将其存储在GOT中。


How is this useful for binary exploitation?  
这对于二进制利用有何用处？

Well, there are two key takeaways from the above explanation:  
好吧，从上面的解释中有两个关键要点：

- Calling the PLT address of a function is equivalent to calling the function itself  
    调用函数的PLT地址相当于调用函数本身
    
- The GOT address contains addresses of functions in `libc`, and the GOT is within the binary.  
    GOT 地址包含 `libc` 中函数的地址，并且 GOT 位于二进制文件中。
    

The use of the first point is clear - if we have a PLT entry for a desirable `libc` function, for example `system`, we can just redirect execution to its PLT entry and it will be the equivalent of calling `system` directly; no need to jump into `libc`.  
第一点的用途很清楚 - 如果我们有一个所需 `libc` 函数的 PLT 条目，例如 `system` ，我们可以将执行重定向到其 PLT 条目，并且它将是相当于直接调用 `system` ；无需跳转到 `libc` 。

The second point is less obvious, but debatably even more important. As the GOT is part of the binary, it will always be a constant offset away from the base. Therefore, if PIE is disabled or you somehow leak the binary base, you know the exact address that contains a `libc` function's address. If you perhaps have an arbitrary read, it's trivial to leak the real address of the `libc` function and therefore bypass ASLR.  
第二点不太明显，但可以说更为重要。由于 GOT 是二进制文件的一部分，因此它始终与基址有恒定的偏移量。因此，如果 PIE 被禁用或者您以某种方式泄漏了二进制基数，您就知道包含 `libc` 函数地址的确切地址。如果您可能进行任意读取，则泄漏 `libc` 函数的真实地址并因此绕过 ASLR 是微不足道的。


Summary 概括

- The PLT and GOT do the bulk of static linking  
    PLT 和 GOT 完成大部分静态链接
    
- The PLT resolves actual locations in `libc` of functions you use and stores them in the GOT  
    PLT 解析您使用的函数的实际位置 `libc` 并将它们存储在 GOT 中
    
    - Next time that function is called, it jumps to the GOT and resumes execution there  
        下次调用该函数时，它会跳转到 GOT 并在那里恢复执行
        
    
- Calling `function@plt` is equivalent to calling the function itself  
    调用 `function@plt` 相当于调用函数本身
    
- An arbitrary read enables you to read the GOT and thus bypass ASLR by calculating `libc` base  
    任意读取使您能够读取 GOT，从而通过计算 `libc` 基数来绕过 ASLR



内容摘自(节选) ：[https://ir0nstone.gitbook.io/notes/types/stack/aslr/plt_and_got](https://ir0nstone.gitbook.io/notes/types/stack/aslr/plt_and_got)