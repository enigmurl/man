# unix

`type <command>` 
- says if it's a shell built in or a binary 

`which <program>`
- locate program in path

`where <program>`
- locate all possible locations in path

`cut <file=stdin>`
- `-b 1-5` byte cut
- `-c 1-5` character cut
- `-f 1,3-4` field cut
    - `-d` set delimeter character (default tab)

`rev`
- reverse each line

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
- '-C' gives more lines of context
- `-v` inverts the match
- `-r` recursively searches
- `-i` case insensitive
- `-l` only print the names of files that match
- `-n` to print line number

`diff <file_a> <file_b>` (finds differences of files, useful for file equality comparison)

`head <file=stdin> [-n lines=10]`
- view first n lines of a file (options for last characters or bytes with `-c` and `-b`)

`tail <file=stdin> [-n lines=10]`
- view last n lines of a file (options for last characters or bytes with `-c` and `-b`)
- `-f` to view the file live

`paste <file1> <file2>`
- combine lines of multiple files into 1 line, separated by tab.

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
    - `-exec grep target {} \;` uses command as filter
    - `-print`
    - `-quit`
    - `-maxdepth`

`watch <file>` continually see if file updates
- `-n <sec>` number of seconds to wait between refresh

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
- the input list is taken as all elements of stdin and then each one is appended as an argument to command. Separated by whitespace
- `-I replstr` instead of appending to end of command, explicitly specify where the replacement should go. Beware that there is size limits on the length of the command
- `-L <max_lines>` use at most max-lines for every single invocation
    - if whitespace for each line do `| tr '\n' '\0' | xargs -0 <command>` (see https://stackoverflow.com/a/28806991)

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

`compress`

`tar`

# os related 

`df` (displays free space)

`du <path>` (disk utility)
- '-H' for human readable

`chmod` (change permissions of a file)

`chown` (change owner of a file)

`umask` (default permissions of file)

`strace` (view system calls made by process)

`mount` (view how file system is comprised)

`sudo <command>` - do command as another user (typically root)
- `sudo -u <user> -i` interactive shell as another user

`uname` (prints details of the current system)

`whoami` (prints the current user)

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

`whois <domain>` (see owner of a domain)

`nslookup <domain>` (lookup ip address)

`ping <server>` (test latency of domain)

`netstat` (shows network status)

`tracerout <domain>` (see route to destination)

`wget <url>` (download data from url)

`curl <url>` (send http request)
- `-X` specifies request type
- `-H` specifies header field
- `-d` specifies data
- `-i` include response headers in output

`nc` (netcat, general utility for working with tcp and udp connections)

`scp` (transfer files from src to destination, possibly on remote machines)

`ssh` (execute arbitrary commands on remote machines)

`ssh-add` (add ssh keys)


# cloud 
useful commands for cloud applications 

`taskset` (force application to run on certain cores)


# other useful binaries

`caffeinate` (macos; ensure system does not sleep)

`binwalk` (search for embedded binaries within an executable)

`od` (octal display)

`xxd`, `bvi` (binary viewer editors)


