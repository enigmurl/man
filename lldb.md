# lldb

## basics

### help
`apropos query`
- search for commands related to query

`help {command}`
- help related to a command

### launch
run 
```
% lldb a.out
> r[un]
```

run with arguments
```
% lldb a.out
> r[un] {args}
```

always run with arguments
```
% lldb -- a.out {args}
> r[un]
...
> r[un]
```

load the executable after starting lldb
```
fil[e] {executable}
```

attach to currently running process. Note, use `-w[aitfor]` if process is currently launching.
```
at[tach] -p[id] {pid}
at[tach] -n[ame] {process_name}
```

### navigation

perform a step over in the current thread
```
n[ext] = thread step-over
```

perform a step into in the current thread
```
s[tep] = thread step-in
```

perform a step into until we hit a function
```
sif {function}
```

perform an instruction level step over
```
ni = thread step-inst-over
```

perform an instruction level step into
```
si = thread step-inst
```

continue program
```
c[ontinue]
```

continue program until this function exits
```
fin[ish] = thread step-out
```

continue current thread until line number
```
th[read] u[ntil] {line} [-f {frame index}]
```

forcefully return from the current function, failing to execute the rest of it
```
th[read] return
```

kill process without exiting lldb
```
process kill
```

quit lldb
```
q[uit]
```

### stack
see thread backtrace
```
th[read] backtrace [--c[ount] count of frames to show] [--s[tart] start index of frames]
bt {count of frames = inf}
```

see information about current frame (roughly equivalent to backtrace of just this frame)
```
fr[ame] i[nfo]
```

select a frame
```
f[rame select] {index = 0}
```

go a few frames up
```
up {count = 0}
```

go a few frames down
```
down {count = 0}
```

### print/eval
print out frame variables
```
    v = fr[ame] v[ar]
    vo // o means showing object description, if applicable
    v [-r {regex of names to look for}]
    v [-g] // for showing global variables
    v [-s] // for showing the scope of each variable
```

print out frame local variables
```
    v -a // no arguments
```

print out frame args
```
    v -l // no locals
```

evaluate and print out expression
```
    expr {expression}
    expr -T {expression} // show nested types
```

create a lldb variable
```
    expr int $foo = 5
    // may be referenced as follows
    expr $foo + 3
```

print out a type
```
    image lookup -t {type name}
```

Note: `p[rint]` is equal to `dwim-print --` and similar to `expr --`, with the difference being their behavior for void arguments

### environment variables

list out environment variables
```
    env
```

set an environment variable
```
    env {KEY}={VALUE}
```

### external tools

run shell
```
shell {shell command}
```

### source listing
list out source code. Note that to move list back to current position, do `f` as an alias for `frame select 0`
```
    list                 // List subsequent lines
    list <file>:<line>   // List around specific file/line
    list <line>          // List current file around specified line
    list <function-name> // List specified function
    list 0x<address>     // List around specified address
    list -[<count>]      // List previous <count> lines (hasn't been working?)
```

### breakpoint
set a breakpoint
```
    br[eak] s[et] -n {function names}
    b {function names}

    // break on lines matching regeex
    b /regex/

    // break on line of file (and column)
    br[eak] s[et] -f {file name} -l {line number} [-c colnum]
    br[eak] s[et] -y {file_name:line[:col}
    b {file_name:line[:col]}
    
    // c++ method name
    br[eak] s[et] -M {method base name}

    // conditional
    br[eak] s[et] -c {'(condition)'} [...]

    // ignore count
    br[eak] s[et] -i {ignore count} [...]

    // one shot
    br[eak] s[et] -o [...]

    // only for certain threads
    br[reak] s[et] -T {thread name} [...]
    br[reak] s[et] -x {thread index} [...]
```

modify breakpoint
``` 
    // options are similar to above
    br[eak] m[odify] {options} {breakpoint ids}
```

list break point
```
    br[eak] list
```

enable breakpoint
```
    br[reak] en[able] {breakpoint num}
```

disable breakpoint
```
    br[reak] di[sable] {breakpoint num}
```

delete breakpoint
``` 
    br[eak] de[lete] {breakpoint num} 
```

you can save and write breakpoints using `br write` and `br read`.

### watch point
set a watch point
```
    // remember that expression should be the address, not the variable
    wa[tch] set e{xpression} -w {read | write | modify | read_write} -s {byte size usually 1 2 4 or 8}
    wa[tch] set v{ariable} -w {read | write | modify | read_write} -s {byte size usually 1 2 4 or 8}
```

modify watchpoint
```
    // add condition
    watch modify -c {'(condition)'} {watch point numbers}

    // set ignore coujnt
    watch ignore -i {count} {watch point numbers}
```

list watch points
```
    wa[tch] list
```

enable watchpoint
```
    wa[tch] en[able] {watchpoint num}
```

disable watchpoint
```
    wa[tch] di[sable] {watchpoint num}
```

delete watchpoint
``` 
    wa[tch] de[lete] {watchpoint num} 
```

## advanced

### hooks

display an expression each time lldb stops
```
    display {expr}
```

add stop hook (more advanced than display)
```
    target stop-hook add [options such as -f -n -l] -o[ne-line] {command}

    // note that hooks are not "compiled" until execution. no errors will be given
    (lldb) target stop-hook add
    Enter your stop hook command(s).  Type 'DONE' to end.
    > expr i + 3
    > DONE
```

similarly, can add command only when breakpoint are encountered
```
    (lldb) breakpoint command add
    Enter your debugger command(s).  Type 'DONE' to end.
    > expr i + 3 
    > DONE
```

we can disable hooks via the following. there are more options for manipulating hooks, see help.
```
    undisplay {hook num}
```

### multi threading

only continue this thread (sometimes hangs if waiting on other threads)
```
    th[read] continue
```

disallow other threads from running when we step
```
    step -m {this-thread | all-threads | while-stepping}
```

switch threads
```
    th[read] select {thread index}
    t {thread index}
```

list all threads
```
    th[read] list
```

### custom formatting

set expression, variable, or type to be in a custom format
```
    expr -f {i[nstruction] [he]x | b[in] | c[haracter] | p[ointer] | d[ecimal]} -- {expression} // see help format for all formats
    v {format} -f {format} // specify how this variable should be formatted
    type format add -f {format} {types} // specify how type should be formatted
```

add custom format for a given type. there are lots of options about how to format a type.
```
    type summary add --summary-string "int = ${var.x}, float = ${var.y}, char = ${var.z%u}" {type}
```

see [https://lldb.llvm.org/use/variable.html] for more on type summaries and formatting

### memory and registers
general purpose memory reading
```
    // note that `expr` always inlines the expression result
    memory read --size {size} [--format {format}] --count {number of chunks of size 'size'} `expr`
    [x/me] r -s4 -fx -c4 0xbffff3c0

    // note that format can take on instruction to print out instructions (or use disassemble)
```

reading registers
```
    register read rax rsp rbp {register list}
    register read --all [--format {format}]
```

writing registers
```
    register write {register} {value}
```

### disassemble

disassemble some portion of the program. there are many options about which section you would like to disassemble exactly; see help disassemble
```
    disassemble
```

### loaded information

find information about a symbol
```
    image lookup -r {regex}

    image lookup -n[ame] main

    // example output:
    1 match found in /Users/manubhat/home/wormpool/cpp/a.out:
            Address: a.out[0x0000000100000f74] (a.out.__TEXT.__text + 0)
            Summary: a.out`main at macro_test.cpp:4:15
```

find information about a symbol given address
```
   image lookup -a 0x1000038ae

    // example output
      Address: a.out[0x00000001000038ae] (a.out.__TEXT.__stubs + 600)
      Summary: a.out`symbol stub for: std::vector<std::pair<long long, long long>, std::allocator<std::pair<long long, long long>>>::operator[](unsigned long) 
```

list shared libraries (and current executable)
```
    image list 
```

list sections along with permissoins
```
    image dump sections
```

### set aliases

create command alias. aliases can take in arguments too using `%argnum`
```
    command alias {alias} {command}

    command alias pltty process launch -s -o %1 -e %1
    pltty /dev/tty0 // example usage
```

### lldbinit
we can store aliases and other common information in `~/.lldbinit`

## references
- [lldb to gdb](https://developer.apple.com/library/archive/documentation/General/Conceptual/lldb-guide/chapters/A3-GDB-Summary.html#//apple_ref/doc/uid/TP40016717-CH4-DontLinkElementID_29)
- [lldb to gdb part 2](https://lldb.llvm.org/use/map.html)

# gdb

## enabling macros for debugging
for gcc:
```
g++ -g3
g++ -ggdb3
```

for clang:
```
clang++ [options] -g3 -fdebug-macro A.cpp
```

## basics

### help
`apropos query`
- search for commands related to query

`help {command}`
- help related to a command

### advanced
- x/5i (addr) 
- x/s (string)
- watch addr
- catch (syscalls)

- disassemble [/s option]

### multithreading

```
    set scheduler-locking on
```
dont run other threads while doing this instruction

# pwndbg

- vmmap
- hexdump (addr) (num bytes)
