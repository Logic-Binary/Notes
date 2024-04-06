这一项指定了当前文件可以运行的机器架构。

| 名称       | 值   | 意义             |
| -------- | --- | -------------- |
| EM_NONE  | 0   | 无机器类型          |
| EM_M32   | 1   | AT&T WE 32100  |
| EM_SPARC | 2   | SPARC          |
| EM_386   | 3   | Intel 80386    |
| EM_68K   | 4   | Motorola 68000 |
| EM_88K   | 5   | Motorola 88000 |
| EM_860   | 7   | Intel 80860    |
| EM_MIPS  | 8   | MIPS RS3000    |
其中 EM 应该是 `ELF Machine` 的简写。

其他值被在未来必要时用于新的机器。 此外，特定处理器的ELF名称使用机器名称来进行区分，一般标志会有个前缀`EF_` （ELF Flag）。例如，在`EM_XYZ`机器上名叫 `WIDGET` 的标志将被称为 `EF_XYZ_WIDGET`。