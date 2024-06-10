# lecture 3

Created: January 24, 2024 6:37 PM

## common terms

| OS terms | general terms |
| --- | --- |
| scripting language | java, php |
| CLI (interface/interpreter) | shell |
| batch file  | program file |
| bash file | handle more complex tasks, such as system administration tasks |
| magic numbers | octal dump |

# basic unix/linux

- unix and linux users are interfaced with system via **command line prompt**, where they can type commands in the command line interface for the system to execute
    - the commands are interpreted by the command line interpreter, called the **shell**
- common shells available on Mac OS/X:
    
    
    | sh (bourne shell, the first unix shell), also on apollo |
    | --- |
    | csh (c shell, following c syntax), also on apollo |
    | tcsh (a newer version of csh), also on apollo |
    | bash (bourne again shell, compatible with sh, but improved), also on apollo
    
    the default shell on apollo is bash |
    | zsh (z shell, backward compatible with bash) |

| unix  | linux  |
| --- | --- |
| family of multitasking, multi-user computer operating systems that derived from the original unix  | a family of free and open-source software operating systems built around the linux kernel |
| source code is not available to the general public  | source code is available to the general public  |
| contains the CLI only  | contains the CLI and GUI |
| used for servers, workstations, mainframes, and high-end computers | used for personal computers |
| not portable  | portable  |

## compiled vs interpreted

- a **compiled language** is when a person writes the code the compiler separates the file and the end result is an executable file — basically, the owner keeps the source code

- **interpreted languages** are different because the code is not compiled first hand — instead, a copy is given to another machine and that machine interprets it

## shell

- most commands used in unix and linux are actually system programs stored somewhere (often stored in `/bin` or `/usr/bin`)
- **prompt**: a `$` or `%` sign that indicates that the computer is expecting to receive your command
    - the convention in unix and linux is to type the name of the file at the prompt to execute it
- `ls`, `cp`, `pico`, `nano`, and `gcc` are all commands that you can find executable files
- a few exceptions that are built-in commands:
    
    
    | cd | change directory |
    | --- | --- |
    | alias | defining a meaning to a command |
    | source | execute a script or batch file |

## directories

- unix and linux file systems are quite similar to windows and mac, in the sense that files are connected to directories (folders) and there are also **symbolic links** (a file pointing to another file)
    
    ```bash
    #to create a directory 
    mkdir comp2432
    
    #delete a directory 
    rmdir comp2432
    
    #change to a directory 
    cd /comp2432 
    ```
    
- your files are stored under a user directory
    
    ```bash
    /home/12345678d
    /webhome/12345678d/public_html
    
    #to show your directory, type pwd
    ```
    
- if the first character is “d”, it is a **directory**

## ls

- to see the list of files, type `ls`
    - there are many options to ls (specified by `-`)
        - e.g. `-o` is an option in cc to specify the output file name
            
            ```bash
            cc lab1.c -o lab1
            ```
            

- `ls -l`
    - gives you a long listing

- `ls -al`
    - show the hidden files

## access privilege

- each file is associated with an **access privilege** — you may see something like `-rwxr —r—` for a file
    
    
    | the first group of four means privilege for “user” (you) |
    | --- |
    | the second group of three means those for “group” (your classmates) |
    | the third group of three means those for “others” (anyone else) |
- `r` means can read, `w` means can write, `x` means can execute
    
    ```bash
    #if you want your classmate to read a file f:
    chmod g+r f
    
    #if you do not want others to read a file f:
    chmod o-r f 
    ```
    
- you could use **octal** to represent the target mode you want to set the file f to (e.g. `chmod 644 f` will assign access privilege `-rw-r—r—` to file f)
- you would see a “`*`” after a file name if it can be executed (with x) under normal unix/linux file systems

![Screenshot 2024-01-31 at 3.02.22 PM.png](lecture%203%200cb3cbaa2913407d826dda0db8f32742/Screenshot_2024-01-31_at_3.02.22_PM.png)

## memory of older commands

- type `history` to see a list of recent commands
- if you want to re-execute a previous command found in history, type `!n`, where n is the number of the command (n could be a large number)
    - to execute the most recent command starting with ca (e.g. cat), type `!ca`
- to execute the last command, just type `!!`
- the uparrow `↑` could also be used in linux bash
- to avoid typing `./hello` to execute hello, you can include the current directory in execution path
    
    ```bash
    PATH = "$PATH:." 
    #appends the current directory "." to the path variable
    ```
    

> “**shell**” is a broad term that refers to any program that provides a CLI 

”**bash**” is a specific type of shell that is widely used in unix/linux
> 

## wildcard

- you could use wildcard to match filenames (***** and **?**)

- `ls lab*`
    - gives you all files with a name beginning with lab

- `ls -l [lab]*`
    - gives you all files with name `labx` (x is a character)
- you could use wildcard symbols to match files in the current or selected directory besides when using `ls` command
    
    
    | $ echo * #display all files |
    | --- |
    | $ cfiles=*.c #no space between =  |
    | $ echo your C programs are $cfiles |
    | $ usefiles=”lab*.c *.doc *.pdf” |
    | $ echo the commonly used files are $usefiles |
- the special command file will let you know the type and other information of a file (or sequence of files)
    
    ```bash
    $ file filename
    $ file filename1 filename2 filename3 ...
    $ labfiles=lab*.c
    $ file $labfiles
    $ usefiles="lab*.c *.doc *.pdf"
    $ file $usefiles
    ```
    

## range specification

- for example, `ls lab[1-5].c` will return all files `labx.c` where **x is 1, 2, 3, 4, or 5** and `ls lab[24].c` will only return `lab2.c` and `lab4.c`, since here `[]` means any character in the set

- if you want to get files lab4.c, lab8.c and lab12.c, you cannot use [], since it **means a single digit** — you have to use `ls lab{4,8,12}.c` instead, where `{}` means any one pattern

## grep

- if you want to look for a particular string within a collection of files, you need to use the `grep` command
- **matching** can be made with respect to patterns
    - patterns are regular expressions, built from some primitives:
        
        
        | "^" | means beginning of line |
        | --- | --- |
        | "$" | means end of line |
        | "." | means any character |
        | [abc] | means any one of a, b, c |
        | [^abc] | means anything except a, b, c |
        | "\" | for escape |
- examples:
    
    ```bash
    #find C programs containing malloc and its usage
    $ grep malloc *.c
    
    #find text files and lines containing PolyU
    $ grep -i polyu *.txt
    
    #print only the names of files with lines beginning with “polyu”
    $ grep -l -i ’^polyu’ *.txt
    
    #look for "poly*" except for polyu without filename
    $ grep –h ’poly[^u]’ *.txt
    
    #-i: case-insensitive 
    #-l: print 
    #-h: supresses prefix of filename 
    
    ```
    

## chain up commands

- a **chain** contains a sequence of commands or even user programs
- `prog1 | prog2 | prog3 | ... | progn`
    - the **output of prog1 is the input to prog2** and so on — the symbol “`|`” is called a **pipe**

- `ls | wc`
    - will count the number of lines, words and characters from a file containing all outputs from ls

- `ls | wc | lpr`
    - will send the output above to a printer (lpr is the command to print with a printer)

# command line interface

- a **process** is a program in execution — process execution is done in a sequential fashion
- in unix/linux, typing a command or a file name will cause the associated program to be executed
    - a process is created for that purpose
    - the program for that command or file will be loaded into the process
    - **arguments** will be passed to the program for execution
- this is **handled by the "shell"**, and it becomes the basic human-OS interface to execute user commands, which is particularly useful if you have a long list of commands to be executed

- the commands could be placed into a file, e.g. aliases and be executed via source command (**source aliases**)

- the file (**batch file**) containing commands can then be considered as a program itself, written in a scripting language and executed at the shell
- the **shell** serves as a command interpreter
    - it prompts a user for the next command, and executes it — then the shell creates a new child process (subshell) to execute the command passed to it
- any program could be the shell and you could write a program to serve as your own unix/linux shell
- **scripting languages**: features in shells make them being like programming languages
    - often called **shell scripts**

> in ms-dos, [command.com](http://command.com) is like the shell
> 

# scripting languages

- usually comes from a tool for quick handling of system operations

- it is considered as a light-weighted programming language

- it is often used for automating repetitive tasks, e.g. backup

- it is also used for prototyping and gluing together other programs
- **strings** are the basic data type, plus **simple arithmetic**
- relatively free of data types and data declaration
- a program written in scripting language is normally **interpreted**

### common scripting languages:

| awk |
| --- |
| javascript |
| perl |
| php |
| python |

### advantages

- fast to write, ready to execute
- a shell script is often smaller than a program
- allow partial execution before complete script is developed
- powerful to directly access OS services and execute commands
- allow string processing and output intermixed with those from running other programs

### disadvantages

- execute more slowly
- weaker data structure and computational support
- syntax often strange and program hard to understand
- protection against accidental mistakes is weak
- occasional bugs around

![Screenshot 2024-01-25 at 8.36.26 PM.png](lecture%203%200cb3cbaa2913407d826dda0db8f32742/Screenshot_2024-01-25_at_8.36.26_PM.png)

# shell

- there are two shells available in almost all unix
    
    
    | bourne shell (sh) | about as old as unix, still basis for many shell scripts and lowest common denominator of unix shells |
    | --- | --- |
    | c shell (csh) | developed in UCB, named due to its similar syntax to C programming language
    
    it has good interactive features and is a preferred shell for CLI |

- there are numerous other shells available
    
    
    | korn shell (ksh) | developed by AT&T and not free |
    | --- | --- |
    | bourne again shell (bash) | it is developed by GNU people and is pretty much the most popular shell for linux systems |
    | almquist shell (ash) | a replacement for "sh" to be executed on resource-constrained environments like mobile phones |
- the command line is the command that you type to the shell prompt, which is typically `%` (for C shell) or `$` (for bash)

- several commands can be given at once by separating them with a semicolon `;`
    
    ```bash
    $ ls; pwd; cal;
    #three processes will be created to execute the three commands sequentially
    ```
    

- several commands can also be chained together via pipes `|` (output being passed as input to the next)

```bash
$ ps -ef | more 
#two processes will be created to execute the two commands in parallel
```

### example

- `wc` stands for word count, and it is used for counting purposes— to find the number of lines, word count, byte and characters count in the file by displaying a four-columnar output
    
    
    | first column shows the number of lines present in a file specified  |
    | --- |
    | second column shows the number of words present in a file |
    | third column shows the number of characters present in a file |
    | fourth column itself is the file name given as an argument |

```bash
$ (ls; cal) | wc
$ (ls; cal 2023) | wc

# there is a subshell for each of the two blocks of commands
# the first block results in three processes one after the other 
# and the second is a process running concurrently with the first block 
```

```bash
$ echo Hello world
#Hello world
$ echo Hello world \on a long line
#Hello world on a long line
```

```bash
$ echo $$  #$$ evaluates to the PID of current shell (not the current command/process)
#1235
$ echo $$
#1235
$ bash #start a subshell
$ echo $$
#1288
```

# variables

- all programming languages must support variables for storing information — in bash, the variables usually store strings (however, integers or array types can also be imposed on variables)
    - variable names are case-sensitive and at most 20 characters long, with letters, digits and "_"
- the value of any variable can be assigned directly, hence there must not be any space before and after “=”
- to print the content of a variable, use `echo $varname`

### environment

- valid across all shells created by the existing shell when it is defined
    - by convention, they are named with upper case letters
- the value of an environment variable is assigned the same way — wherein values set in a subshell cannot be passed back to the parent shell, and to check for the **shell pid**, you can use `$$`

```bash
$ hello=world
$ name="Mickey Mouse"
$ bug = other
#bash: bug: command not found...

$ more = less
#=: No such file or directoryless: No such file or directory

$ echo $hello
#world

$ echo my name is $name
#my name is Mickey Mouse

$ bash #start a subshell
$ echo $name #a blank line is shown
$ exit #exit the subshell
$ echo $name

```

### local

- only valid within the shell when it is defined
    - by convention they are named with lower case letters
- you could set multiple local variables on the same line
    - `var1=str1 var2=str2 ... varN=strN`

```bash
$ name1=John name2=”Mary Anne”
$ echo $name1 and $name2
#John and Mary Anne

$ a=1 b=3 c=5 d=10
$ echo a, b, c, d are $a, $b, $c, $d
#a, b, c, d are 1, 3, 5, 10

$ sum=$a+$b+$c+$d
$ echo sum is $sum
#sum is 1+3+5+10

$ sum=$((a+b+ c +d)) # $(( expr )) evaluates expr
$ echo sum is $sum
#sum is 19

$ let sum2=a+b+c+d // let assigns evaluation
$ echo it is $sum2
#it is 19

$ let avg=sum2/4
$ echo sum and average are $sum2, $avg
#sum and average are 19, 4
```

## common built-in variables

| home | store the full pathname of the home directory: where you will go to (home) when you just type cd |
| --- | --- |
| path | store a list of directories to be searched when running programs or shell scripts, separated by “:” |
| ps1 | store primary prompt string, with a default value of '\s-\v\$’ meaning system-version then $ |
| ps2 | store secondary prompt string, with a default value of '> '— it is used for continuation of input lines |
| pwd | store current working directory set by cd command |
| hostname | store current machine name |
| uid | store internal unix user id |
| ppid | store process id of parent  |
| histsize | store number of lines of history to remember, default to value of 500 |

## special option variables

| history  | setting it will enable command history to be stored, useful for future, default to on |
| --- | --- |
| noclobber  | setting it will prevent overwriting of files when using I/O redirection, default to off |
| ignoreof | setting it will prevent accidental logging out with <Ctrl-D> (end of input), often used when entering data from keyboard, default to off |
| allexport | setting it will automatically export all modified variables, default to off |
- to turn on/off, use set `-o/+o` variable
    
    ```bash
    set –o noclobber #turn on noclobber feature
    set +o history #turn off history storage
    ```
    

# array

- an array is created implicitly by assigning a list of values to the array variable, a value to an element of the array variable, or explicitly via a declare command
    - the elements of the array (strings or integers) are numbered by subscripts starting from zero
- you can access to anywhere in the array, and any uninitialized entry contains an empty string by default

```bash
var=(val1 val2 ... valN) #initialize an array of size N
var[i]=val #assign val to element i in array var
${var[i]} #access element i in array var
${#var[i]} #return length of element i in array var
${#var[*]} #return number of non-null elements in array var
declare –a var #create an array var
declare –p var #print the content of an array var
```

### example

```bash
$ fac=(1 2 6 24 120) #initialize factorial
$ echo element 3, ${fac[3]}, $fac[3]
#element 3, 24, 1[3]

$ i=3
$ echo i/fac[i] are $i, ${fac[i]}, $fac[i]
#i/fac[i] are 3, 24, 1[i]

$ len=${#fac[*]} //len is 5
$ echo sequence ${fac[*]} with length $len
#sequence is 1 2 6 24 120 with length 5

$ echo sequence $fac[*] with length $#fac[*]
#sequence is 1[*] with length 0fac[*]

$ declare -p fac //show info about fac
#declare –a fac=’([0]=”1” [1]=”2” [2]=”6” [3]=”24”[4]=”120”)’

$ fac[6]=5040
$ declare -p fac //show info about fac
#declare –a fac=’([0]=”1” [1]=”2” [2]=”6” [3]=”24”[4]=”120” [6]=”5040”)’

$ echo sequence ${fac[*]} with length ${#fac[*]}
#sequence is 1 2 6 24 120 5040 with length 6 
```

# quotation

- **single quote**
    - the strong quote
    - enclosed string looks like literal
    - no substitution and no execution is done

- **double quote**
    - the weak quote
    - enclosed string is almost like literal
    - substitution is done for variable contents (prefixed with "`$`")
    - execution is done for back-quoted commands
- a special backquote enables a command to be executed (`str`) — in bash, it is more common to put it as `$(str)` instead

### example

```bash
$ echo I need a.out a*
#I need a.out a.c a.out

$ echo ’I need a.out a*’
#I need a.out a*

$ echo ’I need $5.00’
#I need $5.00

$ echo ’I need $500.00 now!!’
#I need $500.00 now!!

$ echo ”I need $5.00”
#I need .00

$ echo ”I need $500.00 now!!”
#I need 00.00 now

$ name=John
$ echo ’Hi $name’
#Hi $name

$ echo ”Hi $name”
#Hi John
```

```bash
$ result=`ps`
$ echo $result # result is a list of words
#PID TTY TIME CMD 8379 pts/1 00:00:00 bash 23142 pts/1 00:00:00 ps

$ result2=$(ps)
$ echo $result2 # result2 is a list of words
#PID TTY TIME CMD 8379 pts/1 00:00:00 bash 23142 pts/100:00:00 ps

$ echo files are `ls`
#files are first.sh lab1A.c lab1B.c

$ p=”$PWD `whoami`- ”
$ echo $p
#/home/12345678d 12345678d-

$ p2=”($PWD) $(whoami)- ”
$ echo $p2
#(/home/12345678d) 12345678d-

$ p3=”$(PWD) $(whoami)- ”
#bash: PWD: command not found...similar command is ’pwd’

$ echo $p3

```

# creating shell scripts

- a shell script contains text line commands and comments (comments are prefixed by "#")
- the first line contains the special pair of characters "`#!`", followed by the shell to be used
    - this "`#!`" is also called a **magic number**, used by unix/linux to identify the program used to interpret the lines in the script
    - when a program is loaded into memory, unix/linux examines this first line
        - if it is **binary**, the program will be executed as a compiled program
        - if it **contains "#!"**, look at the path following the "#!" and start that program as the interpreter
        - this line must be the top line of the script or it will be treated as a comment line (starting with "#")
- we use bash, so the first line is `"#!/bin/bash"`
- enter the script lines with an editor into a file then change the mode to be executable and run it
    - `chmod 700 file` or `chmod u+x file`

> warning: never create a shell script called `test`
> 

## magic behind magic numbers

- use `od –c –x` command in unix to check (ASCII/HEXA)
    
    
    | script file | 23 21 → “#!” |
    | --- | --- |
    | postscript file | 25 21 → “%!” |
    | PDF file | 25 50 44 46 → “%PDF” |
    | java class file | CA FE BA BE (it looks like café babe) |
    | gif file  | 47 49 46 38 39 61 → “GIF89a” / 47 49 46 38 37 61 → “GIF87a” |
    | jpeg file | FF D8 |
    | microsoft office file | D0 CF 11 E0 (it looks like doc file 0) |
    | microsoft .exe file  | 4D 5A → “MZ” |
    | unix executable file  | 7F 45 4C 46 → “*ELF” |
- first shell script —exercise
    
    ```c
    //you must make the file executable
    
    #!/bin/bash
    echo Hello $USER, time is $(date)
    //$(date) allows date executed and replacing output
    echo -n "You must be "
    //-n keeps output on the same line
    whoami
    //whoami is a command/program
    echo Your home is $HOME
    //HOME is a variable
    echo Your calendar for this month is:
    cal //cal is a program
    echo Friends on this computer $(hostname) are:
    who //who and hostname are programs
    echo "Thanks for coming. See you soon!!"
    ```
    

```bash
od -c -x lab1.pdf | more
```

![Screenshot 2024-01-31 at 10.10.11 PM.png](lecture%203%200cb3cbaa2913407d826dda0db8f32742/Screenshot_2024-01-31_at_10.10.11_PM.png)

### big endian and little endian

![Screenshot 2024-01-31 at 10.10.57 PM.png](lecture%203%200cb3cbaa2913407d826dda0db8f32742/Screenshot_2024-01-31_at_10.10.57_PM.png)

# reading input

- a shell script is like a simple program
- it could read input from user via keyboard (standard input) via the command `read`
    - use `-p` to provide a prompt
- to prompt using echo, it is possible to stay on the same line to get the input with `"-n"`

```bash
#!/bin/bash
#this is the greeting script
echo –n ’Please enter your name: ’
read name
echo Greetings to you, $name.

#read –p ’Please enter your name: ’ name
#echo name is $name
#read –a num # read elements into array num 

declare -p num
echo num is ${num[*]}
```

## input/output redirection

- in unix and linux, the standard input is the keyboard and the standard output is the screen
    - **standard error**: there is a second output place for programs, and is normally used only for special system error messages
- you could change them into files, by means of **input/output redirection**
    - to redirect inputs, use `"<"` and to redirect outputs, use `">"`
    
    ```bash
    $ GPA < grade.txt
    $ GPA > grade.txt
    ```
    

### example

```bash
$ GPA < grade.txt
#input read from grade.txt

$ GPA > result.txt
#output written to result.txt

$ GPA &> result.txt
#output and error written to result.txt

$ GPA < grade.txt > transcript.txt
#input read from grade.txt and output written to transcript.txt

$ GPA < grade2.txt >> transcript.txt
#input read from grade2.txt and output appended to transcript.txt

$ GPA < grade2.txt &>> transcript.txt
#input read from grade2.txt and output and error appended to transcript.txt

$ echo $USER is using $(hostname) at $(date) >> usage.log
#log the current information and append to usage.log
```

# pipes

- the unix/linux pipe could be considered as a use of I/O redirection, passing output from one program as input to another
    - processes are working **concurrently** using pipe but are working **sequentially** when using I/O redirection

```bash
$ prog1 | prog2
#effect like prog1 > tempfile1; prog2 < tempfile1

$ prog1 | prog2 | prog3
#effect like prog1 > tempfile1; prog2 < tempfile1 > tempfile2; prog3 < tempfile2

$ prog1 < infile | prog2 > outfile
#effect like prog1 < infile > tempfile1; prog2 < tempfile1 > outfile
```

## arithmetic

| operator | description | example | evaluates to |
| --- | --- | --- | --- |
| + | addition  | echo $(( 20 + 5 )) | 25 |
| - | subtraction  | echo $(( 20 - 5 )) | 15 |
| / | division  | echo $(( 20/5 )) | 4 |
| * | multiplication  | echo $(( 20*5 ))  | 100 |
| % | modulus  | echo $(( 20%3 )) | 2 |
| ++ | post increment   | x=5
echo $(( x++ )) | 
5 |
| — | post decrement  | x=5
echo $(( x— )) | 
5 |
| ** | exponentiation | y=2
y=3
echo $(( x**y )) | 

8 |

```bash
#!/bin/bash 

read -p "Enter two numbers: " x y 
ans=$(( x + y ))
echo "$x + $y = $ans"
```

### example

```bash
#!/bin/bash
read -p "What was your score? " score
echo You get a score of $score
if [ $score -ge 85 -a $score -le 100 ]; then
	echo You got an A grade!
elif [ $score -ge 70 ]; then
	echo You got a B grade
else
	echo You better study!!
fi
```

# if-statement

- the simplest form of conditional is the if statement where, if the expressions evaluates to true (i.e. non-zero), commands after the `then` keyword are executed until `fi` is reached— otherwise, those after `else` are executed
    - the `if` statement may be nested as long as every single if statement is terminated with a matching `fi`
- use the fortran like relational operators and standard logical operators
    - (e.g. `eq (==), -ne (!=), -gt (>), -ge (>=), -lt (<), -le (<=), -a (&&), -o (||), !`)

# case-statement

- value in the case expression is matched against the expressions, under each pattern (terminated with ‘`)`’)
- a pattern can be a **constant**, contains wildcards and case sensitive

- use of `;`; at the end of a pattern/command transfers execution to the end of case statement

- use of `;;&` at the end of a pattern/command allows next pattern to be tested (can match with multiple patterns)

```bash
#!/bin/bash
read –p ”Which subject have you performed worst? ” code 
case $code in
	comp1*) echo ”$code is just a level 1 subject.” ;;
	comp2*) echo ”$code is a level 2 subject.” ;;
	comp[34]*) echo ”$code is a senior level subject.” ;;
	comp[56]*) echo ”$code is too advanced for you.” ;;
	comp*) echo ”$code is not valid comp code.” ;;
	*) echo ”$code is not a valid code.” ;;
esac
```

# for loop

- the **for command** is followed by a variable and a list (the list can be stored in a variable or generated by a command)
- the list is saved and each element is considered in turn — each element is assigned to the variable and commands in the loop body are executed
    - note that changes to the list inside the loop will not affect the number of times the for loop is executed

```bash
#!/bin/bash
courselist=(1011 2021 2432) # list from an array
for course in ${courselist[*]}; do
	if [ $course -lt 2000 ]; then
		echo level 1 course $course
	else echo level 2 course $course
	fi
	done
	for file in `ls`; do # list from command ls
		echo file $file exists
	done

# level 1 course 1011
# level 2 course 2021 2432
```

- a simplified format of for loop involves a simple list generation like the standard C for loop, allowing expressions to be included
    
    ```bash
    #!/bin/bash
    for f in *.c; do #simple list
    	echo $f is a C file
    done
    for i in {1..10}; do #list of numbers
    	echo i is $i
    done
    for i in {1..20..3}; do #list of nonconsecutive numbers
    	echo i is $i
    done
    for ((i=1; i<=10; i++)); do # like C for loop
    	echo number is $i
    done
    for item; do # list of input arguments
    	echo argument is $item
    done
    
    # number is 1 2 3 4 5 6 7 8 9 10
    ```
    

# while loop

- the while loop evaluates an expression— as long as it is true, commands below while will be executed until the done statement is reached, then control will return to the while expression, which is evaluated again
- when the while expression is false, the loop ends and control starts after the done statement
    - you could use the break statement to jump out of the loop

```bash
#!/bin/bash
read –p ”Please enter a number: ” num
ctr=1 sum=0
while [ $ctr -le $num ]; do
	let sum=sum+ctr
	echo $ctr
	let ctr++
done
echo ”The sum of $num numbers is $sum.”
```

# until loop

- the until loop is exactly the same as while loop, except for the opposite condition to terminate the loop— as long as it is false, commands below until will be executed
- when the until expression is true, the loop ends and control starts after the done statement
    - you could use break statement to jump out of the loop
- both while and until loops in bash are **pre-test loops** that test before execution (0 to infinite times)

```bash
#!/bin/bash
read –p ”Please enter a number: ” num
ctr=1 sum=0
until [ $ctr -gt $num ]; do
	let sum=sum+ctr
	echo $ctr
	let ctr++
done
echo "The sum of $num numbers is $sum"
```

# file testing

- there is a built-in set of options for testing the attributes of files (e.g. a directory, a plain file, or a readable file)
    
    
    | -e | file exist  |
    | --- | --- |
    | -d | file is a directory |
    | -f | file is a plain file  |
    | -r | file could be read by owner |
    | -w | file could be written  |
    | -x | file could be executed |

```bash
#!/bin/bash
for f in *; do # for each file
	if [ -d $f ]; then echo $f is directory
	fi
	if [ -f $f -a -x $f ]; then echo $f is executable file
	fi
	if [ ! -w $f ]; then echo $f could not be modified
	fi
done
```

# command line argument

- the bash assigns command line arguments to positional parameters and there is no specific limit on the number of arguments that can be assigned
    - **positional parameters** are numbered variables, where the script name is assigned to $0 (inclusive of the ./ part of the path) and all words following the script name are assigned to `$1, $2, $3 ... ${10}, ${11}`, and so on

- the value `$#` contains the total number of arguments

- the variable `$@` contains the full list of arguments

- the variable `${!#}` contains the last argument

## special variables

| $*
$@ | positional parameters (arguments to script), starting from one
when the expansion occurs within double quotes, it expands to a single word or separate word respectively |
| --- | --- |
| $# | number of positional parameters (arguments) |
| $? | exit status of the most recently executed command |
| $$ | process ID of the shell |
| $0 | name of the shell or shell script |

### example

```bash
#!/bin/bash
# call this script testarg.sh
echo ”This script is called $0”
if [ $# -eq 0 ]; then
	echo ”No argument”
else
	echo ”The number of arguments : $#”
	echo ”The first argument : $1”
	echo ”The last argument : ${!#}”
	echo ”The full list : $@”
	echo ”The fifth argument : $5” 
fi
$ ./testarg.sh
#This script is called ./testarg.sh
#No argument

$ testarg.sh one two three
#This script is called ./testarg.sh
#The number of arguments : 3
#The first argument : one
#The last argument : three
#The full list : one two three 
#The fifth argument :
```

# exit status

- after a command or program terminates, it would return an exit status to the parent process— it is the number within exit() system call
    - the exit status is a number between 0 and 255
        - by convention, when a program exits, if the status returned is zero, the command was successful in its execution
        - when the exit status is non-zero, the command has failed in some way
- in bash, the status variable (`$?`) is set to the value of the exit status of the last command that was executed, and the success or failure of a program is determined by the programmer who wrote the program with the number returned in exit()
    - **a return value of -1 from a program is equivalent to 255, but 256 means 0**

```bash
$ ps
$ echo $?
0
$ abc
bash: abc: Command not found...
$ echo $?
127
```