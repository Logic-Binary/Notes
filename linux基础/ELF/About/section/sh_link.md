#### sh_link & sh_info

当节区类型的不同的时候，sh_link 和 sh_info 也会具有不同的含义。

|sh_type|sh_link|sh_info|
|---|---|---|
|SHT_DYNAMIC|节区中使用的字符串表的节头索引|0|
|SHT_HASH|此哈希表所使用的符号表的节头索引|0|
|SHT_REL/SHT_RELA|与符号表相关的的节头索引|重定位应用到的节的节头索引|
|SHT_SYMTAB/SHT_DYNSYM|操作系统特定信息，Linux 中的 ELF 文件中该项指向符号表中符号所对应的字符串节区在 Section Header Table 中的偏移。|操作系统特定信息|
|other|`SHN_UNDEF`|0|

