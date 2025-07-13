# Preprocessing
- `gcc -E file.c -o file.i` - run only the preprocessor stage.
- `clang -E file.c -o file.i` - clang

**Flags**:
- `-I<dir>`: Add include search path.
- `-D<macro>=[value]`: Define a macro.
- `-U<macro>`: Undefine a macro.

# Compiling
- `gcc file.c -o file.o` - compile to executable
- `gcc -c file.c -o file.o` - compile to object file
- `clang file.c -o file.o` - clang

**Flags**:
- `-Wall -Wextra`: cnable common warnings.
- `-std=c99`: Use specific C standard.
- `-O0/-O1/-O2/-O3/-Ofast/-Og` optimization level
- `-g/-ggdb3` debugging symbools; `-ggdb3` allows for macros
- `-fPIC`: position independent code, useful for shared objects
- `-isysroot `xcrun --show-sdk-path` set the sdk path for clang
- `-Wl,<linker_key>[,<linker_value>]` forward an option to the linker

**CP Flags (mostly gcc)**
- `-pedantic -Wshadow -Wfloat-equal -Wconversion -Wlogical-op -Wshiftoverflow=2 -fstack-protector-all`: extra warnings
- `-D_GLIBCXX_DEBUG -D_GLIBCXX_DEBUG_PEDANTIC` better debug assertions
- `-D_FORTIFY_SOURCE=2` avoid buffer overflows
- `-fsanitize=address,undefined` sanitizer options

**Pragmas**:
- `#pragma unroll `

# Archiving
Combines multiple object files into a static library.

- `ar rcs libname.a file1.o file2.o` - Create a static library.

**options**
- `ar t libname.a`: List contents of a library.
- `ar x libname.a`: Extract files from a library.

# Linking
Combines object files and libraries into an executable. Specifically, missing symbols are resolved using the symbol tables.

## `ld` (Linker)
Links binary object files and resolves symbols. Use a linker script to explicitly specify how different sections should be positioned in the ELF/executable as well as in main memory once the program is loaded.

- `ld -o output file1.o file2.o` - Link an object file to create an executable.

**Flags**:
- `-L <dir>`: Add library search path.
- `-l <name>`: Link against `libname.so` or `libname.a`.
- `-[-e]ntry <symbol>` linux, set some symbol as the entry point
- `-shared`: on linux, creates a shared library
- `-s`: on linux, strip symbols
- `--verbose`: for linux, view the default linker script
- `-stack_size 0x1000`: Set stack size on macOS.
- `-rpath <dir>`: macos, specify runtime library search path.

# Inspection/Post-Build

## demangling c++ symbols
Translates mangled C++ names into readable names.
```bash
c++filt <mangled_symbol>
# example
c++filt __ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEC1B8ne180100ILi0EEEPKc
> std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_string[abi:ne180100]<0>(char const*)
```

get a list a list of strings from an object or binary file
```bash
strings <file>
```

objdump analyzes all types of elf files
```bash
# disassemble
objdump -d file.o
# elf headers
objdump -h file.o
# all symbols
objdump -t file.o
# debugging info
objdump -g file.o
```

mach-o object viewer
```bash
# list dynamic runtime dependencies
otool -L file
# disassemble
otool -tV file
# print headers
otool -h binary
```

readelf also deals with elfs
```bash
# header info
readelf -h file.o
# inspect sections
readelf -S file.o
# symbols
readelf -s file.o
# view dynamic (analyze runtime dependencies)
readelf -d binary
```

symbol viewer for elf
```bash
# list symbols
nm file.o
# list dynamic symbols
nm -D file.o
# only global symbols
nm -g file.o
# demangle symbols
nm -C file.o
```

sign and verify signatures on macos
```bash
# sign
codesign -s "Developer ID Application: Manu Bhat (TEAMID)" binary
# verify
codesign -v binary
```

### modifying executable
macos, update runtime search path
```bash
# new may be e.g. @executable_path/../Frameworks
install_name_tool -add_rpath <new>
```

linux, updating runtime search path
```bash
# print current value
patchelf --print-rpath <binary>
# update
patchelf --set-rpath <new-path> <binary>
```
