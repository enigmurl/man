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

    # outputs the stem (none here)
    echo $*

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
In general, command line arguments override variable declarations. Use `override` to not use this behavior


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
ifneq ($(findstring i, $(MAKEFLAGS)))
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


## references
- https://makefiletutorial.com/

# cmake

## configuration

Standard build pipeline
```
mkdir build
cmake ../Step1
# or make -j .
cmake --build .
```

## flags
`-S' explicitly tell source directory
`-B` explicitly tell build directory
`-E` adds capabilities to cmake (e.g. md5sum)

`--trace-source="filename"` - for debugging cmake files
`--trace-expand` lets you look at the values of variables


## core
first line looks like this
```cmake
cmake_minimum_required(VERSION 3.15)
```

for the top level cmake lists, we have to state our project (metadata)
```cmake
project(PorjectName VERSION 1.0
                    DESCRIPTION "description"
                    LANGUAGES CXX)
```

### making executable
here, one is the name of the target and executable. target is the current built target of focus
```cmake
add_executable(one two.cpp three.h)
```
headers are mainly for ides

### making library
```cmake
add_library(one [STATIC | SHARED | MODULE] two.cpp three.h)
```

### linking library
public = others can see MyLibExample as well
private = others won't see 
interface = only others see
```cmake
target_link_libraries(MyExample [PRIVATE|PUBLIC|INTERFACE] MyLibExample)
```

### importing
add subdirectory
```cmake
add_subdirectory(directory)
```

set module path, which is a list of helper cmake lists
```cmake
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
```

List of [cmake modules](https://cmake.org/cmake/help/latest/manual/cmake-modules.7.html)

One way to import is via git submodules, in which case we have a extern directory with a list of submodules in it. We can also download at build time. Fetch content is another method
```cmake
FetchContent_Declare(
  catch
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG        v2.13.6
)
```

### find_package
I used to find this quite confusing, but all it's doing is finding the location of some package (with possibly some configuration options) and setting variables to that location so that we can link against it easily.

```cmake
find_package(Boost 1.50 REQUIRED COMPONENTS filesystem)
```

Searches in two possible modes. First starts with module mode, and falls back to config mode.
1. module mode - searches for the file `FindBoost.cmake` external to the project, used when the package was not made for cmake, meaning that this file uses some sort of heuristics
2. config mode (ideal) - searches for the file `BoostConfig.cmake` internal to the project, used when the package was made for cmake. Also searches a lot more locations

Note that find_package tries to get all possible information. If we just need the library we may simply search for `find_library`

related is `find_program`, which can be used to find certain command line utitlies
```cmake
find_program(VAR name)
```

### installation
the install command specifies the "final" build artifiacts that we should share with other users
```
install(TARGETS MyLib
        EXPORT MyLibTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include
        )
```

By then running this command, all of the final files that we want to distribute.
```
cmake --install . --prefix "root_path"
```

We can also install someone else's package into the system binaries.


### properties
instance attributes of a particular target

```cmake
set_property(TARGET TargetName
             PROPERTY CXX_STANDARD 11)

# can set multiple properies at once
set_target_properties(TargetName PROPERTIES
                      CXX_STANDARD 11)

get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
```
Useful properties
- CXX_STANDARD
- CXX_CLANG_TIDY
- CXX_INCLUDE_WHAT_YOU_USE


### c/c++
flag -fPIC
```
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
# for just a target
set_target_properties(lib1 PROPERTIES POSITION_INDEPENDENT_CODE ON)
```

add directories to include list
```
include_directories(${CAPNP_INCLUDE_DIRS})
```

some variables (although some recommended to not use since not cross platform)
```
CMAKE_CXX_FLAGS
```

## cmake general syntax

### variables
```cmake
set(VAR_NAME value)
# access
${VAR_NAME}
# list
set(LIST_NAME value1 value2)
list(APPEND variable value)
```

cache variables are variables that are set if not already set (useful if defined on command line)
```cmake
# type can be e.g. STRING
# FORCE, if supplied always sets the variable (effectively making it global and ignoring scope)
set(MY_CACHE_VARIABLE "VALUE" CACHE TYPE [FORCE] "Description")

# bool is very common and has custom syntax
option(MY_OPTION "This is settable from the command line" OFF)
```

env variables, generally recommended against
```cmake
set(ENV{ENV_NAME} value)
# acces
$ENV{ENV_NAME}
```

globs
```
file(GLOB SRC_VAR_NAME "src/*.cpp" "src/*.hpp")
```

print variable
```cmake
message(STATUS "MY_VARIABLE=${MY_VARIABLE}")
```

### control flow
```cmake
if(variable)
    # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
else()
    # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, or ends in `-NOTFOUND`
endif()
```
### function
```cmake
function(SIMPLE REQUIRED_ARG)
    # note this is how to print
    message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGN}")
    # equivalent of return
    set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Output: ${This}")
```

we can also declare macros, which share the parent scope
```
macro(FetchContent_MakeAvailable NAME)
    FetchContent_GetProperties(${NAME})
    if(NOT ${NAME}_POPULATED)
        FetchContent_Populate(${NAME})
        add_subdirectory(${${NAME}_SOURCE_DIR} ${${NAME}_BINARY_DIR})
    endif()
endmacro()
```

### generator expressions
seems relatively niche, but a way to do control flow at build/install time
```
# syntax $<BOOLEAN:value>
target_compile_options(MyTarget PRIVATE "$<$<CONFIG:Debug>:--my-flag>")
```

### configure files (output)
niche; command that substitutes cmake variables (useful for configuring headers)
```
configure_file(src dst)
```

### read file (input)
get configuration variables from a header
```
# Assuming the canonical version is listed in a single line
# This would be in several parts if picking up from MAJOR, MINOR, etc.
set(VERSION_REGEX "#define MY_VERSION[ \t]+\"(.+)\"")

# Read in the line containing the version
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/include/My/Version.hpp"
    VERSION_STRING REGEX ${VERSION_REGEX})

# Pick out just the version
string(REGEX REPLACE ${VERSION_REGEX} "\\1" VERSION_STRING "${VERSION_STRING}")

# Automatically getting PROJECT_VERSION_MAJOR, My_VERSION_MAJOR, etc.
project(My LANGUAGES CXX VERSION ${VERSION_STRING})
```

### running commands

```cmake
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if(NOT GIT_SUBMOD_RESULT EQUAL "0")
        message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
    endif()
endif()
```

## testing
Testing can be done via conditional inclusion of the test directory based upon options
```cmake
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME AND BUILD_TESTING)
    add_subdirectory(tests)
endif()
```

We can register test targets as follows
```
add_test(NAME TestName COMMAND TargetName)
```


## references
- https://cliutils.gitlab.io/modern-cmake/chapters/basics.html
- https://cmake.org/cmake/help/latest/guide/tutorial/A%20Basic%20Starting%20Point.html
