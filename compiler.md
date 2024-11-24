# tools

Demangle c++ symbol names
```
c++filt __ZNSt3__112basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEC1B8ne180100ILi0EEEPKc
> std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_string[abi:ne180100]<0>(char const*)
```

```
objdump
```

# clang

# ld

Setting stack size (macos)
```
    ld -stack_size 0x1000
```

# gcc
