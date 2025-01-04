# zsh

## path

path is special variable that dictates search locations of commands

## variables

`U=blah` set U to be blah

for BASH
`${U}` or sometimes `$U` expands the contents of variable U, possibly into multiple words
`"$x"` keeps the expansion as a single word, and does not recursively expand special characters
for ZSH
`$u$ means “take the value of the variable, except remove it if it's empty." (for array, apply this to all elements)
`"$u"$ means “take the value of the variable"

`export U=blah` export as envariable variable

`U=blah command` run command with `U` as environment variable

Note the following about eager evaluation
```zsh
B=0
A=$B
B=4
echo $A
```

Utilities for extracting basenames
`${var#pat}` min match of pat removed from head
`${var##pat}` max match of pat removed from head
`${var%pat}` min match of pat removed from tail
`${var%%pat}` max match of pat removed from tail
Most of the above applies to arrays, where the operation is done element wise

## quoting
single quotes generally allow no form of substitution
double quotes generally allow some forms of substitution, but word splitting is always disabled
- parameter expansion, command substitution and arithmetic substitution are whats allowed

## arrays
```zsh
array=(element1 element2 element3)
# access first element
echo $array[1]
# slice (inclusive)
echo $my_array[1,3]
# concatenate
array+=(element4)
# set
array[2]=updated_element
# splice
array[2]=(a b)

# length
${#array}
```

expansion
```zsh
array=("" apple cherry)

function print() { echo $1 }

# expand each word, but remove empty strings 
# NB: in bash, this is just the first element
print $array
# expand each word, but remove empty strings 
print ${array[@]}
# joins the array into a single word
print "$array"
# do not remove empty strings from the resultant words
print "${array[@]}"

# gives
apple
apple
 apple cherry

# ^ above is empty line
```

## function

```zsh
function name() {
    echo $1 $2 $3
    # local variable
    local xyz=abc
    # expr list
}

# invocation
name arg1 arg2 arg3
```

## control

for
```zsh
for x in 1 2 3
do echo $x
done

for x in 1 2 3; do echo $x; done

for ((i = 1; i < 4; i++)); do echo $i; done
```

if
```zsh
if listi1; then[;] listt1;
[ elif listi2; then listt2; ]
...
[ else listt3; ]
fi

if true; then
    echo "hi"
fi
```
while
```zsh
while test; do somecommand; done

while test
    do somecommand
done
```
select
```zsh
select choice in $list; do
  echo $choice
done
```

switch statement
```zsh
case word in
[(] pattern1[|pattern2...]) [;] list ;;
...
esac

# from bashed
case "${char}" in
		A)
			input_key='Up'
			return 0
			;;
		B)
			input_key='Down'
			return 0
			;;
		*)  # default case
		    ;;
esac
```

## operators

highest binding is pipe `|` which connects output of left command to right. Stderr is the same for both (terminal)

`;` is lowest precedence and equivalent to a new line (executes next command regardless of success of previous)

`$()` subshell (`expr` also works but is not recommended), also referred to as command substitution

`()` is simply a subprocess that only reports back the error code

`$(())` is arithmetic subshell

`(( ))` is simply an arithmetic subprocess that only reports back the error code

`X{a,b}` = `Xa Xb`

`X{a..c}` = `Xa Xb Xc`

`X{1..3}` = `X1 X2 X3`


## redirection
`expr < in_file` redirects in_file to input of expr

`expr > out_file` redirects output of expr to out_file

`expr >> out_file` redirects output of expr to out_file, in append mode

`expr &> out_file` directs both stdout and stderr to file

`expr 2>&1` redirects stderr to wherever stdout is currently pointing 

`<(expr)` runs the command `expr` and puts the output into some arbitrary file. Then `<(expr)` is replaced with the file name

## test

`false` is a command that always give exit code 1

`true` is a command that always give exit code 0

[[ ]] is an alias of test command

file operations
```zsh
-e #  exists
-f # is file
-d # is directory
```

string and arithmetic operations
```zsh
== (left) string matches (right) pattern
=~ string matches regex
-eq numerical comparison
-lt numerical less than
-gt numerical greater than
< string less than
> string greater than
```

## wildcards

`*` means any string
`?` means any character
`[class]` 
`[^negated_class]`
`(A|B)` pattern A or pattern B
`<a-b>` range of any integer from a to b, inclusive. defaults for a and b are 0 and infinity

expand local directory `*.rs`
expand arbitrary many directories `**/*.rs`

niche non posix modifiers

`*(/)` only directories

`*(.)` only directories

`*(@)` only symlinks

`*(*)` only executable files


## shell script options
`set -e` exits program upon any error

## special variables
`$$` is the pid of the shell

`$?` last return code

`$@` is all of the arguments. Typically use `"$@"` to avoid

`$1` is the first argument to the program or this function

`$n` is the nth argument to the program or this function

`$_` last argument to last command

## jobs

Run a job in the background
```zsh
sleep 10 &
```

put a job onto foreground, where we can reference an existing job with `%n`. 
```zsh
fg [job]
```

to put a process in the background, press control z

list out running jobs with
```zsh
jobs
```

## history

`history` print out history with
`history 1` prints out full history

control r lets you do history reverse search

`!!` is the last command

```zsh
brew install python
# invalid perms..
sudo !!
```

`!<expr>` is similar to inline control r where you only search for the command (no arguments)

## signals
Use control c to send sig int
Use control d to send eof

## references
- https://www.bash2zsh.com/zsh_refcard/refcard.pdf
- https://unix.stackexchange.com/questions/306111/what-is-the-difference-between-the-bash-operators-vs-vs-vs
- https://unix.stackexchange.com/questions/95952/zsh-possibly-adding-quotes-to-variable-value-works-in-bash-though
- https://zsh.sourceforge.io/Guide/zshguide05.html
