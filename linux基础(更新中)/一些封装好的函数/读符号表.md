```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <elf.h>


void read_symbols(char* file_name) {
    FILE* file = fopen(file_name, "rb");
    if (!file) {
        perror("Failed to open file");
        exit(1);
    }

    // Read ELF header
    Elf64_Ehdr header = {0};
    fread(&header, sizeof(header), 1, file);

    // Read section header table
    fseek(file, header.e_shoff, SEEK_SET);
    Elf64_Shdr* sections = malloc(header.e_shentsize * header.e_shnum);
    fread(sections, header.e_shentsize, header.e_shnum, file);

    // Find symbol table section
    Elf64_Shdr* symtab_section = NULL;
    char* strtab = NULL;
    for (int i = 0; i < header.e_shnum; ++i) {
        if (sections[i].sh_type == SHT_SYMTAB) {
            symtab_section = &sections[i];
            // Find corresponding string table
            strtab = malloc(sections[symtab_section->sh_link].sh_size);
            fseek(file, sections[symtab_section->sh_link].sh_offset, SEEK_SET);
            fread(strtab, sections[symtab_section->sh_link].sh_size, 1, file);
            break;
        }
    }

    // Read symbol table entries
    if (symtab_section) {
        Elf64_Sym* symbols = malloc(symtab_section->sh_size);
        fseek(file, symtab_section->sh_offset, SEEK_SET);
        fread(symbols, symtab_section->sh_size, 1, file);

        // Iterate through symbol table entries
        for (int i = 0; i < symtab_section->sh_size / symtab_section->sh_entsize; ++i) {
            Elf64_Sym* symbol = &symbols[i];
            printf("Symbol name: %s\n", strtab + symbol->st_name);
        }

        free(symbols);
    } else {
        printf("No symbol table found.\n");
    }

    free(sections);
    free(strtab);
    fclose(file);
}

int main(int argc, char* argv[]) {
   
    if (argc != 2) {
        printf("Usage: %s <shared_library>\n", argv[0]);
        return 1;
    }

    read_symbols(argv[1]);

    return 0;
}

```



