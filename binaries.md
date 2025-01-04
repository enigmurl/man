# unix

`cut <file=stdin>`
- `-b 1-5` byte cut
- `-c 1-5` character cut
- `-f 1,3-4` field cut
    - `-d` set delimeter character (default tab)

`tr` (translate stdin)
- `tr xy ab` translates x to a, y to b
- `-d <string>` deletes characters from string in stdin. Use `-C` to complement. Note we can use syntax such as `[:alpha:]`
- `-s <string>` for each character in string, multiple occurences of that character are compressed into 1

`sort <file=stdin>` (sorts input record/linewise)
- `-r` to reverse
- `-k field_ind` sort by key
- `-n` sort by integer comparison
- `-g` sort by floating comparison

`uniq <file=stdin>` (filters duplicate lines)

`grep <pattern> <file=stdin>` (filter records of file)
- `-v` inverts the match
- `-r` recursively searches
- `-i` case insensitive
- `-l` only print the names of files that match

`diff <file_a> <file_b>` (finds differences of files, useful for file equality comparison)

`head <file=stdin> [-n lines=10]`
- view first n lines of a file (options for last characters or bytes with `-c` and `-b`)

`tail <file=stdin> [-n lines=10]`
- view last n lines of a file (options for last characters or bytes with `-c` and `-b`)

`cat <file1=stdin>*`
- concatenates multiple files, or simple stdin

`echo <args>`
- display args to stdout

`find`
- directory filtering
- useful options
    - `-o` or
    - `-type [f|d]`
    - `-name "*.rs"`
    - `-iname "*.rs"` (case insensitive)
    - `-path "*.rs"`
    - `-exec grep target {}` uses command as filter
    - `-print`
    - `-quit`
    - `-maxdepth`

`sed <pattern> <file=stdin>` perform replacements
- pattern 
    - 's/old/new'
    - 's/old/new/g' - global modifier that applies replacement multiple times per record
    - 's/old/new/i' - case insensitive
    - '1,$s/old/new' - address range
    - '1,$d' - delete range
- `-i` performs operation in place

`tee [outputs]*`
- prints out stdin and also writes it to the output files

`nl <file=stdin>`
- prepred line number to file

`wc <file=stdin>` 
- display number of characters, words, lines of file
- options `-c, -w, -l`

`xargs <command> <initial args>`
- the input list is taken as all elements of stdin and then each one is appended as an argument to command
- `-I replstr` instead of appending to end of command, explicitly specify where the replacement should go. Beware that there is size limits on the length of the command

`dirname <path>`
- gets the directory of a path

`basename <path>`
- gets the filename of a path
- `-s .png` suffix to strip from the path

`join <file_a> <file_b>`
- recordwise concatenates output

`awk`
- very complex, can be done for general text processing

# archiving

`zip`

`unzip`

`tar`

# os related 

`df`

`du`

`chmod`

`chown`

`umask`

`strace` - view system calls

`mount` - view how file system is comprised

`sudo` - do command as another user (typically root)
- 

`uname` - prints details of the current system

`whoami` - prints the current user


# monitoring

`kill <pid>` 
- send a signal to a process 
- `-9` for force kill

`ps` 
- '-x' include processes not on terminal (usually what you want)

`top`/`htop`
- live statistics about cpu usage

`iostat` 
- statistics about io devices

`vmstat` 
- statistics about virtual memory

`uptime` 
- view how long a machine has been up

# internet

See owner of a domain
`whois`

Lookup DNS records

`ping`

`netstat`

`wget`

`curl `

`nc`

`scp`

`ssh`


# other useful binaries

`caffeinate` - macos

`binwalk`
- search for embedded binaries

`od`
- octal display

`xxd`, `bvi`
- binary viewer editors


