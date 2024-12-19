# make

## general syntax
you must use tabs, not spaces
```
targets: prerequisites
    command
    command
```

when we run `make target`, make will find the rule that can generate the target file. If all the prerequisites have not changed, then it does nothing. Otherwise, it recursively generates the necessary prerequisites.

By default, `make` with no arguments runs the first target.

### variables

explicit variable syntax
```make
# force set
var_name := file
# append
var_name += file2
# set if not set already
var_name ?= file
# `lazy` behavior; dont recommend
var_name = ${file}


target1: $(var_name)
    command

# alternate syntax
target2: ${var_name}
    command
```

Note that we can escape `$` by using `$$`. There is no way to explicitly quote, so get things such as commas or whitespace in certain circumstances, use variables.

Variables are inherited from the shell environment automatically. Conversely, use `export var` to have the variable be exposed to the environment of subcommands


`*` can be used for glob patterns. Recommended to wrap in wildcard function to deal with the case of no matches.
```make
# Print out file information about every .c file
print: $(wildcard *.c)
	ls -la  $?
```

Automatic variables to know:
```make
hey: one two
	# Outputs "hey", since this is the target name
	echo $@

	# Outputs all prerequisites newer than the target
	echo $?

	# Outputs all prerequisites
	echo $^

	# Outputs the first prerequisite
	echo $<
```

Special variables related to C programs. Note that there is automatically a rule to create executables from object files, and object files from c/c++ files using the appropriate flags.
```make
CC: Program for compiling C programs; default cc
CXX: Program for compiling C++ programs; default g++
CFLAGS: Extra flags to give to the C compiler
CXXFLAGS: Extra flags to give to the C++ compiler
CPPFLAGS: Extra flags to give to the C preprocessor
LDFLAGS: Extra flags to give to compilers when they are supposed to invoke the linker
```

target and pattern specific variables:
```make
%.c: one = cool

blah.c:
	echo one is defined: $(one)

other:
	echo one is nothing: $(one)
```

## static patterns
`%` shows up in a few contexts
- in matching mode, it matches a few characters and becomes the stem
- in replacing mode, it takes the step and substitutes it

```make
# pattern rule that in effect is static pattern with all possible targets
%.o: %.c
    g++ -o $@ $^
```

static pattern: 
```make
targets: target_pattern: pre_req_pattern:
    commands

objects = foo.o bar.o all.o
# Syntax - targets ...: target-pattern: prereq-patterns ...
# In the case of the first target, foo.o, the target-pattern matches foo.o and sets the "stem" to be "foo".
# It then replaces the '%' in prereq-patterns with that stem
$(objects): %.o: %.c
    $(CC) -c $^ -o $@
```

filters can be used to filter a list, useful in static patterns
```make
obj_files = foo.result bar.o lose.o
src_files = foo.raw bar.c lose.c

# only .o
$(filter %.o,$(obj_files)): %.o: %.c
	...

# only .result
$(filter %.result,$(obj_files)): %.result: %.raw
	...
```

## commands
use `@` at the start of a line to silence a command from being printed when its being run. Useful for `@echo`
use `-` at the start of a line to supress errors

The `SHELL` variable is by default `/bin/sh`, but can be changed. Note that each command runs in a new shell.

Use `$(MAKE)` instead of `make` so that flags will be recursively passed on

## command line 
In general, command line arguments override variable declaraitions. Use `override` to not use this behavior


## conditional
ifeq statement syntax
```make
ifeq ($(foo), ok)
	echo "foo equals ok"
else
	echo "nope"
endif

ifdef foo
	echo "foo is defined"
endif

# niche
# search for the "-i" flag. MAKEFLAGS is just a list of single characters, one per flag. So look for "i" in this case.
ifneq (,$(findstring i, $(MAKEFLAGS)))
	echo "i was passed to MAKEFLAGS"
endif
```

## functions
[gnu function list](https://www.gnu.org/software/make/manual/html_node/Functions.html)

syntax:
```make
$(fn, arguments)
# or
${fn, arguments}
```

example:
```make
bar := ${subst not,"totally", "I am not superman"}
# more advanced subst
foo := a.o b.o l.a c.o
one := $(patsubst %.o,%.c,$(foo))
# syntax sugar for above
two := $(foo:%.o=%.c)
# This is the suffix-only shorthand, and is also equivalent to the above.
three := $(foo:.o=.c)
```

shell function (replaces newlines with spaces)
```make
    @echo $(shell ls -la)
```

filter function
```make
obj_files = foo.result bar.o lose.o
filtered_files = $(filter %.o,$(obj_files))
# multiple conditions
filtered_files = $(filter %.h %.c,$(src_files))
# invert conditions
filtered_files = $(filter_out %.o,$(obj_files))
```

error function
```make
$(error "bad input")
```

## other features

`include` includes the content of a makefile, if it exists
use backslash at the end of a line for multiline commands
```make
echo This line is too long, so \
		it is broken up into multiple lines
```

## special targets

`all` is not recognized as special, but by convention it can be placed first so that its default target
`clean` is also not special, but by convention it cleans up the build directory

`.DELETE_ON_ERROR` - if any command fails, the target is deleted if it was inadvertently created. Recommended.

`.PHONY` - this target is not actually a file. The command should be run even if the file does not exist

`.PRECIOUS` - by default intermediate targets are deleted. This says that we should keep it.

`.EXPORT_ALL_VARIABLES` - all variables will be passed as environment variables


### references
- https://makefiletutorial.com/

# cmake

