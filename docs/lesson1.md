# 1. óra

## Background knowledge

In Computer Architectures exercises, we will be dealing with writing shell scripts in Unix
based operating systems. What is considered a Unix-based system, and what are shell scripts?

The original AT&T Unix operating system was developed in the 1960s and has served as the basis for many later operating systems. Some of these are actual Unix operating systems (Solaris), while others are Unix-like systems: Mac OS X and the numerous Linux distributions.

During your computer science studies, it is worth getting to know at least one Linux operating system more closely (so it is worth installing it on your own home computer, even on a virtual machine [WSL on Windows]). At the time of writing this note, Linux Mint is installed on the department computers (Mint is a Linux distribution). In addition, the following Linux distributions are worth mentioning:

- **Ubuntu.** The various Ubuntu versions have now become more widespread than Linux Mint (Ubuntu 18.04, 20.04, 22.04, 24.04, 26.04; versions are numbered according to the year of their release). Easy to install, user-friendly. Perfect for home practice.
- **Arch Linux.** It is famous for being so minimalist that the user has to install practically everything non-essential themselves.
- **Manjaro Linux.** It's based on Arch Linux, they just tried to make it more "user friendly".
- **Kali Linux.** Specialized in digital analysis and vulnerability testing Linux.

When it comes to managing the shell, it doesn't matter which Linux operating system you're using. But what is a shell?

The core of operating systems is the **kernel**. The *kernel* is responsible for performing basic functions (such as process management, memory management, and file system operations; more on these in the Operating Systems topic). The *shell* is a command interpreter that converts the user's text commands into commands that the kernel can understand.

So we will think of the shell as a command interpreter running in a terminal. The image below shows a terminal with a shell running in it.

![terminal screenshot](img/terminal1.png)

In the terminal shown above, the **Bash** command interpreter is running, and the commands create
two folders; practice and the lesson1 folder inside it, and then
enter the lesson1 folder. You can also run other shells in a terminal, not just Bash
(other shells, for example: sh, zsh). Windows operating systems also have shells, but there we can only encounter them as Powershell and Command Line. These have a different syntax than
Bash. We will write Bash scripts in the exercises.

**Bash:** the name is a play on words. The current shell standard is based on the original AT&T Unix shell standard, developed by Stephen Bourne. Bash = **B**ourne
**A**gain **Sh**ell.

One of the great strengths of Linux-based systems is that they have a large number of pre-made "commands" (about 700-1000). Commands are also called "tools", after all, most of them are external utilities. Here are some examples of what the commands are suitable for:

- `cd` – command to enter another directory
- `mkdir` – command to create a directory
- `wc` – command to count words, lines and characters
- `grep` – select lines matching a given text pattern
- etc.

Later on, we will learn about such commands and their usage through tasks. The commands can be combined with each other and can be used to perform complex tasks.

First, open a terminal (Ctrl + Alt + T) running bash (presumably
this is the default). We will do the rest of the tasks in the terminal.

## Examples

### Example 1
Let's create a folder that we will use for the exercises.
Let's name it `practice`. In this folder, let's create another one, whose name
will be `lesson1`. Let's go into this folder!

This can be achieved, for example, with the following commands:
```bash
mkdir practice
mkdir practice/lesson1
cd practice/lesson1
```

In the example above, we observe the following:
- We issued 3 commands, pressing enter between them, so we issued the commands one by one
. The shell interpreted the commands line by line, so the shell is an interpreter.
- In the commands, the first word is **command name**, the remaining words are **arguments**.
In general, the form of a simple command is:
`command_name command_argument_1 command_argument_2 command_argument_n`
The arguments are separated from each other and from the command name by spaces. This gives some sense
to the definition in the theoretical textbook that
*"a command is a sequence of words separated by whitespace characters*".
- Command names are usually abbreviations of some meaningful word pair.
`mkdir` = make directory = create a folder (or more technically, a directory).
`cd` = change directory = change directory, or *change directory*.
- On the command line, we can see what directory we are currently in.

In this regard, it can also be observed that folder names are separated by a `/` (slash) symbol. This is the case on Unix-based systems, while in Windows this symbol is `\`
(backslash).
### Example 2

Let's print out where we are in the directory system, with the absolute path!

We can also use the `pwd` command (print working directory) for this. This will print out the current
directory (working directory).

Type the command!

```bash
pwd
# we should get something like this: /home/username/practice/lesson1
# The hashmark (#) means a comment in bash
# So whatever you write after the # will not be interpreted by the command line
```

The beginning of the output you get does not match what you read in the terminal. If you look,
the `~` character has been replaced with another "path" (or *path*). The `~` character
is an abbreviation for your own user root folder. For me, `~` means `/home/bbalage`,
while for other users it will be different.

The beginning of the output you get does not match what you read in the terminal. If you look,
the `~` character has been replaced with another "path" (or *path*). The `~` character
is an abbreviation for your own user root folder. For me, `~` means `/home/bbalage`,
while for other users it will be different.

### Example 3

Let's enter the root directory and list its contents!

We can use the `ls` command to list the contents of a directory.
The `ls` command can optionally
be specified as the directory to list. If nothing is specified, it lists the contents of the
working directory.


```bash
cd /
ls
#If you don't want to enter the folder, but just want to list
# its contents, you can do this in one step
# with the following command:
ls /
```

The picture illustrates what we will get:

![terminal screenshot](img/terminal2.png)

What we see are system folders. Just to highlight a few of them:
- `bin`: the binary files, i.e. the programs, are located here. Many of the programs used during the semester can be found in this folder. Of the above, the `mkdir`, `pwd`
and `ls` commands are also here, but the `cd` command is not, because the `cd` command is implemented in the `bash` command interpreter itself (part of the language), while the others are external
utilities. That is why we say that the `mkdir`, `pwd` and `ls` commands are **external** commands, and `cd` (and other commands implemented in bash) are **internal** commands. The significance of this is that external commands can be installed separately, as a kind of
plugins, and it is possible that not all external commands are available on a given system
(because they were not added to the installer, and the system administrator did not install them separately).
- `dev`: short for "devices" and contains files describing physical or logical devices
(backup storage, cpu cores, terminals, joystick input, keyboard, mouse, etc.)
- `root`: the home folder of the system user.
- `home`: the folder containing the home folders of other users (and our own user).

### Example 4

Let's go into the `practice/lesson1` folder and create a file called whatever.txt!


```bash
cd ~/practice/lesson1
touch whatever.txt
```

The `cd ~/practice` command does not matter where we issue it, because the ~ symbol is a shorthand for the home directory of the currently logged in user, so `~/practice` is a full path, not a relative one.

The `touch` command updates the last accessed and last modified dates of a file given as an argument. If the file we specified does not exist, it creates it. Thus, we usually use the `touch` command to create files.

### Example 5

Let's list the entire contents of the `practice/lesson1` folder, including the owners of the files in it!

```bash
cd ~/practice/lesson1
ls -la
ls -la ~/practice/lesson1 # if we issue the command like this, then it doesn't matter what the working directory
```

We see that we have issued the `ls` command a little differently than before.
The operation of a command can be modified using switches. A switch comes after the command, but its location can vary from command to command and switch to switch. Almost every
command has switches, some of them quite a lot, and they are quite complicated.
To see what the switches of a command are, what they are for, and how they work,
we can either use the Internet or the **manual page**.

The following command will show the *manual entry* of the `ls` command:

```bash
man ls
```

You can scroll down the manual page with the arrows and exit by pressing the q character.
There is a manual entry for almost every command, and we use them!

Let's try the `ls` command switches!

```bash
ls -l # specify the switches with a hyphen
ls -a # this is another switch
ls -la # this is the two previous switches combined
# (as if we had issued both)
```
The manual tells us the following:

- The `l` switch displays more information about files and folders, not just their names.
- The `a` switch also displays folders that start with or consist of `.`. These are hidden files or folders.
- The `la` switch is a combination of the two: more information and names that start with a dot.

Names that start with a dot tend to be about some kind of extra stuff that we don't want to be visible to a user who is just clicking around in a *file explorer* interface. These can be command line files, configurations, or practically anything else that we decide to name with a dot.

### Example 6

Create a `tmp` folder inside the `practice/lesson1` directory and list its contents!
What are the contents of the folder?

```bash
mkdir ~/practice/lesson1/tmp
# if the working directory is ~/practice/lesson1 then this is enough:
# mkdir tmp
ls -a ~/practice/lesson1/tmp # full path
ls -a tmp # relative path; depends on the working directory
          # (i.e. "where we are")
```

We see that even the empty list has two entries: `.` and `..`
The entry with a dot points to the list itself, while the entry with two dots points to the parent list (which contains the current list). So, we can go back to the `practice` list with the following command:

```bash
cd ..
```

Such backward directory names can also be chained. The following will "back" two layers:

```bash
cd ../..
```

And so on. We didn't do anything with the following, because `.` refers to the folder it is in.

```bash
cd .
```

### Example 7
Delete the `tmp` directory!

```bash
cd ~/practice/lesson1
rmdir tmp
# if we omit the cd, we can of course do this:
# rmdir ~/practice/lesson1/tmp
# the same thing happens, only in one case it is a full path, in the other case
# relative path is the type of link
```

`rmdir` = remove directory; a name that is sufficiently eloquent to need no explanation.

### Example 8
Delete the file `whatever.txt`!

```bash
rm ~/practice/lesson1/whatever.txt
```

`rm` = remove; is also descriptive. It is worth noting, however, that this command does not
put the files in the trash, but actually deletes them. We will not be able to recover what we got rid of from the trash.

### Example 9
Create the following folder structure inside the `~/practice/lesson1` folder (leave all files
empty):

```
|
|-src (directory)
 |- main.c (file)
 |- util.h (file)
 |- util.c (file)
|-assets (directory)
 |-textures (directory)
  |-xy.png (file)
  |-wz.png (file)
  |-readme.txt (file)
 |-maps (directory)
|-build (directory)
 |-release (directory)
 |-debug (directory)
```

A jegyzékeket könnyen létrehozhatjuk az alábbi módon:

```bash
cd ~/practice/lesson1
mkdir src
mkdir assets
mkdir assets/textures
mkdir assets/maps
mkdir build
mkdir build/release
mkdir build/debug
```

However, we didn't learn anything new, and we issued two more commands than necessary!

If we read the manual entry for the `mkdir` command (`man mkdir`), we will learn that it has a `-p` switch, which also tells it to create the parent directory. For example, the
command below will fail with an error by default:

```bash
mkdir build/release
```

This is because there is no `build` folder in which to create the `release` folder.
The `-p` switch modifies the behavior in such a way that in cases like this, the
parent folder is also created by the command (`-p` as *parent*). By issuing the commands like this:

```bash
cd ~/practice/lesson1
mkdir src
mkdir -p assets/textures
mkdir assets/maps
mkdir -p build/release
mkdir build/debug
```

We create files simply with the `touch` command!

```bash
touch src/main.c
touch src/util.c
touch src/util.h
touch assets/textures/xy.png
touch assets/textures/wz.png
touch assets/textures/readme.txt
```

### Example 10 
Let's list the contents of the folders, in a character graphically arranged form!

```bash
tree
# yes, that's the command. Let's issue it in the lesson1 folder!
```

The `tree` command is not installed on all systems, but it does exactly what we need. Let's check if the resulting folder structure is like the screenshot below. If it is not, then we have messed something up.

![terminal screenshot](img/terminal3.png)

### Example 11
Delete all png files in the `textures` folder!

```bash
rm assets/textures/*.png
```

The asterisk will match any text (more on regular expressions later).
Use the `tree` command to check if the png files (and only those) are gone.
You can also use the `ls assets/textures` command (whichever is more convenient) to do this.

### Example 12
Create a `utils` folder inside the `src` folder and move the `util.c` and
`util.h` files into it!

```bash
mkdir src/utils
mv src/util.h src/utils/util.h
mv src/util.c src/utils/util.c
```

`mv` = move. Its operation is quite simple. The first argument of that file or folder
is the path we want to move, and its second argument is the new location
valid path.

So, the file that we have accessed so far on the `src/util.h' path, after issuing the command
We will access it via `src/utils/util.h`.

### Example 13
Rename the `readme.txt` file in the `textures` folder to `readme_sprites.txt`.

```bash
mv assets/textures/readme.txt assets/textures/readme_sprites.txt
```

We usually use the `mv` command for renaming, as mentioned above.

### Example 14
Copy the `readme_sprites.txt` file to the `maps` folder as `readme_maps.txt`!

```bash
cp assets/textures/readme_sprites.txt assets/maps/readme_maps.txt
```

`cp` = copy. Its operation is the same as the `mv` command, only here we copy, not move.

### Example 15
Delete the `maps` and `build` folders!

```bash
rm -r assets/maps
rm -r build
```

The `-r` switch means *recursive*, and its effect is that if you give the `rm` command a folder, it will recursively delete the entire contents of the folder (and the contents of all folders in the folder) before deleting the folder itself.

## Summary

We learned the following commands:
- `cd` changes the working directory (in cooking language, it enters another folder).
- `pwd` displays the current working directory (in cooking language, the directory we are in).
- `ls` displays the contents of a directory.
- `mkdir` creates a new directory.
- `touch` updates the last access date and last modification date of a file, and
if the file does not exist, it creates it.
- `rmdir` deletes a directory.
- `rm` deletes files and folders.
- `tree` draws the folder structure graphically.
- `cp` copies files and folders.
- `mv` changes the path of files and folders (i.e. moves and/or renames them).
- Open `man` manual entry for a command.

## Assignments

Independent practice assignments.

### Assignment 1

Let's go on a reconnaissance trip in the file system! Let's look into other users' home folders!
Find the most likely place where they will put their valuable submissions!
If their files are readable by everyone, then we can look at them. If they are writable, then we can
delete them. Lesson: pay attention to permissions (we will look at them in the next lesson).

### Assignment 2

Change the last modification date of `util.h`, but only the modification date!
Note that the command modifies both the last access and the last modification dates by default! To solve this assignment, read the appropriate switch from the manual entry of the `touch`
command (independent investigation).

### Task 3
Create an `include` folder inside the `lesson1` folder. Copy the `util.h`
file into it, but issue the command so that it will only be copied if the file with the same name in the target folder
does not exist or is outdated! (copy only if necessary) Find the
appropriate switch from the `cp` command manual entry!