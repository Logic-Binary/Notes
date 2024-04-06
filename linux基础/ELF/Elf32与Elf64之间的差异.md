
# Elf header
struct elf32_header <font color="#ffff00">size:0x34</font>
struct elf64_header <font color="#ffff00">size:0x40</font>
二者的头结构体大小不同，原因在于
- 头结构体中标识了程序的入口点(4/8字节)
- 程序头的文件偏移(4/8字节)
- 节头的文件偏移(4/8字节)

# program header
struct program_table_entry34_t program_table_element <font color="#ffff00">size:0x20</font>
struct program_table_entry64_t program_table_element <font color="#ffff00">size:0x38</font>
二者程序头的大小也不相同,有六处不同的地方
ps:段属性的存放位置发生了变化
- Elf64_Off p_offset_FROM_FILE_BEGIN 该段文件偏移(4/8字节)
- Elf64_Addr p_vaddr_VIRTUAL_ADDRESS 该段的虚拟地址(4/8字节)
- Elf64_Addr p_paddr_PHYSICAL_ADDRESS 该段的物理地址(4/8字节)
- Elf64_Xword p_filesz_SEGMENT_FILE_LENGTH 该段的文件大小(4/8字节)
- Elf64_Xword p_memsz_SEGMENT_RAM_LENGTH 该段在内存中的大小(4/8字节)
- Elf64_Xword p_align 该段的对齐大小(4/8字节)


.symtab中的条目大小以及字段顺序不一样
- x86 size：0x10
	`st_name`：符号的名称在字符串表中的偏移量。
	`st_value`：符号的值或地址，取决于符号的类型。
	`st_size`：符号的大小，如果是函数，则表示函数的字节数。
	`st_info`：包含了符号的类型和绑定信息。
	`st_other`：保留字段，目前未使用。
	`st_shndx`：符号所在的节的索引。

- x64 size : 0x18
	`st_name`: 符号名称在字符串表中的偏移量，通常为 4 字节。
	`st_info`: 符号信息，包含符号的类型和绑定信息，通常为 1 字节。
	`st_other`: 符号的其他信息，通常为 1 字节。
	`st_shndx`: 符号所在节的索引，通常为 2 字节。
	`st_value`: 符号的值，通常为 8 字节。
	`st_size`: 符号的大小，通常为 8 字节。
