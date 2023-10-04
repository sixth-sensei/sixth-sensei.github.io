# Black Hat Bash: Unveiling the Dark Side of Command Line Power

_**Inspired by the works of Dolev Farhi and Nick Aleks**_

In the world of computer programming and system administration, the Bash shell stands as a powerful and versatile tool, revered for its capabilities in managing and automating tasks across various operating systems. However, just as a tool can be harnessed for noble purposes, it can also be exploited for nefarious ones. Enter the realm of 'Black Hat Bash' – a dark corner of the digital universe where the potent capabilities of the Bash shell are wielded not for the greater good, but for malicious intent. In this article, we delve into the shadowy world of Black Hat Bash, providing a concise and cautionary 'cheatsheet' that explores the tools, techniques, and ethical implications associated with its misuse.

## **The Shebang Line**

Start a script with the shebang line

```bash
#!/bin/bash

--snip--
```

Some Linux distributions place the bash interpreter in different system locations, and this shebang line will attempt to find that location. which is much more portable.

```bash
#!/usr/bin/env bash

--snip--
```


The shebang line can also take optional arguments to change how the script executes.

```bash
# print all commands and their arguments as they are executed to the terminal useful for debugging

#!/bin/bash -x


# create a restricted bash shell, which restricts certain potentially dangerous commands e.g rm -rf 

#!/bin/bash -r

```


We can also pass arguments to the bash interpreter using the syntax shown below

```shell
$ bash -r myscript.sh
```


> **Note :** whether we pass arguments to the bash interpreter on the command line or on the shebang line won’t make a difference.


## **Comments**

Comments are parts of a script that the bash interpreter won’t treat as code, and they can improve the readability of a program.

```bash
#!/bin/bash

# This is my first comment!
# Bash scripting is fun...
```


## **Commands**

We can write a very simple script that prints out **"Hello World!"** using the `echo` command

```bash
#!/bin/bash
echo "Hello World!"
```

To run the script, save the file as {whatever}.sh, open the terminal, and navigate to the directory where the script resides. If you saved the file in your home directory, you run the following set of commands:

```bash
# cd : command to change directories.
# chmod : set the executable (u+x) permissions
# ./ : represents the current directory where the scripts should be run

$ cd ~
$ chmod u+x helloworld.sh
$ ./helloworld.sh

Hello World!
```


> **Note :** Instead of `./` we can also run the scripts with `bash hellowworld.sh` , furthermore the script doesn’t have to be set with an executable permission (+x).


## **Debugging**

the `-n` parameter. This parameter will read the commands in the script but won’t execute them, so if there are any syntax errors, they will be shown on the screen.

```bash
$ bash -n script.sh
```


the `-x` parameter to turn on verbose mode, which lets you see commands being executed 🚈

```bash
$ bash -x script
```

start debugging at a given point in the script, by using the `set` command in the script itself 

```bash
#!/bin/bash
set -x

--snip--

set +x
```

You can think of set as a valve that turns on and off a certain option. The first command sets the debugging mode (set -x) while the last command (set +x) disables it.

## **Basic Syntax**

The most basic bash scripts are just lists of Linux commands collected in a single file. For example, you could write a script that creates resources on a system and then prints information about these resources to the screen.


```bash
#!/bin/bash
# All this script does is create a directory, create a file
# within the directory, and then list the content of the directory.

mkdir mydirectory
touch mydirectory/myfile
ls -l mydirectory
```


- we use `mkdir` to create a directory named **"mydirectory"** .
- we use the `touch` command to create a file named **myfile** within the directory.
- we run the `ls -l`  command to list the contents of the **mydirectory** directory.
- The output of the script looks as follows:

```bash
--snip--
-rw-r--r-- 1 user user 0 Feb 16 13:37 myfile
```


## **Variables**

Variables are names we assign to memory locations that hold some value, and they act like placeholders or labels.

The following rules govern the naming of bash variables:
•They can include alphanumeric characters.
•They cannot start with a numerical character.
•They can contain an underscore (_).
•They cannot contain whitespace.

### **Assigning and Accessing Variables**

We can assign a variable directly on the terminal

```bash
$ text="i love bash"
```

we can use this variable in some command. For example, here we use the `echo` command to print the variable to the screen:

```bash
$ echo "i love pentesting and ${text}"

i love pentesting and i love bash
```

We can also assign the output of a command to a variable using the command substitution syntax `$()`, placing the desired command between the two parentheses

```bash
$ root_directory="$(ls -ld /)"
$ echo "${root_directory}"

drwxr-xr-x 19 root root 4096 Sep 29 21:12 /
```

> **Note :** You shouldn’t leave whitespace around the assignment symbol (=) when creating a variable. e.g

```bash
$ book = "this is an invalid variable assignment"
```

### **Unassigning Variables**

You can unassign assigned variables using the unset command, as shown here:

```bash
$ text="i love bash"
$ unset text
$ echo "${text}"
```


### **Scoping Variables**

Global variables are those available to the entire program. But variables in bash can also be scoped so that they are only accessible from within a certain block of code. These variables are called local variables and are declared using the local keyword. This script shows how local and global variables work:


```bash
#!/bin/bash

PUBLISHER="No Starch Press"

print_name(){
  local name
  name="Black Hat Bash"
  echo "${name} by ${PUBLISHER}" 
}

print_name

echo "The variable ${name} will not be printed here because it is a local variable."
```

- We assign the value **"No Starch Press"** to the variable **"publisher"**
- created a function called **"print_name()"**
- Within the function, we declare a local variable called **"name"** and assign it the value "**Black Hat Bash**"
- Then we call the **"print_name()"** function and attempt to access the **"name"** variable as part of a sentence to be printed using **"echo"**.

If we save the script and run it we get :

```bash
$ ./local_scope_variable.sh
$ chmod +x local_scope_variable.sh

Black Hat Bash by No Starch Press
The variable will not be printed because it is a local variable.
```

> **Note :** The **echo** command at the end of the script file will result in an empty variable, as the name variable is locally scoped to the **print_name()** function, which means that nothing outside of the function can access it. So, it will simply return without a value.

