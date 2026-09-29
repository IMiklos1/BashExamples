Lesson 3
===

In this lesson, we will learn new commands and how to combine them. Before we start
combining commands, let's learn how to edit files in the terminal!

## nano (terminal text editor)
`nano` is one of the many terminal text editors that are commonly available on a
Unix system (others include emacs and vim). Nano is a text editor that works
entirely in the terminal, so no separate window is needed to use it.
The mouse is almost completely useless as well.

With `nano`, we can edit files without opening a new window.
This is useful when we only want to write a few lines to a file, or when we want
to edit a file as an administrator.

Open a terminal and create a `lesson3` folder inside the `practice` folder!
Enter this folder and open a file named `tmp.txt` with `nano`!

```bash
mkdir practice/lesson3
cd practice/lesson3
nano tmp.txt # command name and file path
```

Write any text into the file! For example: "Blablabla; blablabla". We can save the file
with Ctrl + O (O, as in write out). After that, we can exit by pressing
Ctrl + X (X, as in exit).

If we run the `ls` command afterwards, we can see that the file was created. If we also
want to view its contents, we can either open it again with the `nano` text editor or
simply print its contents to the terminal. We can do this with the `cat` command.

```bash
cat tmp.txt # prints the contents of the file to the terminal
```

## Combining commands
Unix commands follow the principle that one command should perform **one** essential task.
Therefore, if we want to perform a more complex task, we probably will not find a single
command that solves it for us. Instead, we have to combine existing commands in some way.
For combining commands, let us first consider the following methods:

- `command1 && command2` -> if command 1 succeeds, command 2 is executed as well
- `command1 || command2` -> if command 1 fails, command 2 is executed
- `command1 | command2` -> redirects the output of command 1 to the input of command 2
- `command1; command2` -> executes command 2 after command 1. This is not really
combining commands, because we simply ran two commands one after the other, except that
we did it on one line instead of two.

In addition, we can redirect the command's output to another "stream".
This is useful, for example, when we want to write to a file or simply "throw away"
the output. Let us look at the following redirections:

- `command > streamname` -> redirects the command's output to the stream named `streamname`.
A typical case is when `streamname` is a file; then the output overwrites the contents
of the file (the original contents are deleted).
- `command >> streamname` -> the same as the previous operator, except that the command's
output is appended to the stream instead of overwriting it. This is especially noticeable
with files, because the original contents are not deleted as they are with the previous operator.

Notice that by default we also write to a stream, the *standard output* stream (*stdout*,
identifier 1). The *standard input* stream (*stdin*, identifier 0) is also familiar, as is
the *standard error* stream (*stderr*, identifier 2). We will discuss these after learning
a few useful commands.

To demonstrate the above, let us learn the `echo` command (like an echo)!
It simply prints whatever we give it:

```bash
echo "Hello World!"
# prints Hello World! and adds a newline character at the end
```

Let us look at combining commands through examples!

## Examples

### Example 1
Try to enter a directory named `tmp`, and print an error if it fails!

```bash
cd tmp || echo "Error"
```

In the example above, the first command is `cd tmp`. The two vertical bars mean that if
the first command fails, the second command is executed. The second command is `echo "Error"`.
Therefore, if `cd tmp` fails, `echo "Error"` is executed; otherwise it is not.

Run the command when the `tmp` folder does not exist and examine the result!
Then create the folder and run the command again!

### Example 2
Create a folder named `dir_1`, and enter it if successful (otherwise do nothing)!

```bash
mkdir dir_1 && cd dir_1
```

The above is very similar to the previous example, except that here the second command is
executed only if the first one succeeds.

### Example 3
Try to enter a folder and, if successful, print the current working directory;
otherwise create the folder! (print the current working directory with `pwd`)

```bash
cd dir_2 && pwd || mkdir dir_2
```

The command above may require a short explanation. It consists of three parts:
1. `cd dir_2` -- attempts to enter the folder
2. `pwd` -- prints the current working directory
3. `mkdir dir_2` -- creates the folder

Execution can proceed in two ways, depending on whether we can enter the folder.
1. **The first command fails:** the command after `&&` is not executed (because it is
executed only if the preceding command succeeds), but the command after `||` is executed
because an error occurred before it. Therefore, the folder is created.
2. **The first command succeeds:** the command after `&&` is executed, probably
successfully, because the `pwd` command has no meaningful way to fail here. The command
after `||` is not executed because there was no error before it.

Additional cases would arise if `pwd` could fail.

Note that normally, if we run the command twice in a row, it will fail the first time
but not the second time.

**Question:** what happens if the `dir_2` folder already exists, but we do not have execute permission for it?

### Example 4
Try to enter the `dir_3` folder and, if that fails, create it and then enter it!
Naturally, solve all of this with a single line of commands!

**Solution:**

```bash
cd dir_3 || mkdir dir_3 && cd dir_3
```

The examples above could be combined further, but it is not worthwhile. Even commands
consisting of three parts are uncommon, since they are not very easy to understand at a
glance. We more often redirect streams from one command to another. Let us look at some
examples of this as well!

### Example 5
Create a file named `xyz.txt` and use `echo` to write our name into it!

```bash
touch xyz.txt
echo "Johnny Bravo" > xyz.txt
```

Note that `touch` is actually unnecessary, because if the file does not exist when the
second command is run, the stream redirection creates it.

```bash
echo "Johnny Bravo" > xyz.txt # If xyz.txt does not exist,
                              # it is created here.
```

We can view the contents of the file with either `nano` or `cat`.

### Example 6
Create a file named `www.txt` and use `echo` to write our name and Neptun code into it!
Then append the contents of this file to the end of the `xyz.txt` file!

```bash
echo "Donald Duck" > www.txt
echo "QUACK1" >> www.txt
cat www.txt >> xyz.txt
# Let us also view the contents of xyz.txt:
cat xyz.txt
```

As a reminder, `>` deletes the original contents of the file, while `>>` appends to
the file's existing contents.

### Example 7
List the contents of the current folder and write the returned text to the `content.txt` file!

```bash
ls > content.txt
```

Lesson: the output of any command can be redirected to a file.

### Example 8
Sometimes we install something on the computer and text is printed during installation
that we do not need or want to see. Let us simulate this with the `ls -la` command!
Run the command, but do not display its output!
(Yes, this does not make much sense, but we have not learned a command whose output
would be useful to ignore. So let us examine how to ignore output and forget that we
would certainly not use this command for that purpose!)

```bash
ls -la > /dev/null
```

Explanation: `/dev/null` is a file on the system (located in the `dev` folder inside the
root folder). The special feature of this file is that anything written to it disappears.
It is specifically used to redirect streams that should be ignored. We will not use it for
anything, but if we see it in a tutorial, we should not be surprised!
It is a special file that has no contents and never will.

### Example 9
Try to create `dir_3` again, even though it already exists! The error message should
not be displayed!

We notice that the following solution does not work:

```bash
mkdir dir_3 > /dev/null
```

This is where the fact that the system distinguishes three standard channels becomes
important:
- **stdin:** short for *standard input*. Text entered by the user is sent here.
Its identifier is 0.
- **stdout:** short for *standard output*. Data printed by programs is sent here
(`echo`, `ls`, etc.). So far, this is the channel we have mostly seen displayed in our
terminal.
- **stderr:** short for *standard error*. Error messages printed by programs are sent here.
These are also displayed in the terminal, which is why we do not see the difference
between it and *stdout*.

The solution above does not work because the `>` operator redirects only the *stdout*
channel to the `/dev/null` file, not the *stderr* channel.

Solution:

```bash
mkdir dir_3 2> /dev/null
```

In this case, we wrote the identifier of the channel we want to redirect before the `>`.
Note that the following two commands do exactly the same thing:

```bash
ls > random_something.txt # redirects stdout to the file
ls 1> random_something.txt # also redirects stdout,
                           # but writes its identifier explicitly
```

By default, the `>` and `>>` operators redirect channel 1 (standard output).

We will return to channel redirection later, when we write error messages ourselves.

## The grep command
The third way to combine commands is made possible by the `|` symbol (also known as the
"pipe operator" in Bash). **This is used most often.** As we read earlier, this operator
**redirects the output of one command to the input of another command**. It therefore looks
a little as if it connects the two with a pipe (hence the name "pipe operator").

This operator is most often used together with the `grep` command. It is a very
(very, very) useful command, so we will spend a lot of time on it.
It essentially does this: **it selects from its input the lines that match a given pattern**.

Let us look at some examples! First, create a file named `people.csv`.
Its contents should be as follows:

```
Name;Birthdate;Phone;Skill
Robert Bob;1997.09.12.;06201975555;IT
Zsuber Driver;1988.10.11.;06304445555;Driving
Hatori Hanso;1966.01.11.;06301234555;Smithing
Rinaldo Orson;2001.05.24.;06709330000;IT
Travis Camel;1970.10.01.;06301717171;Horses
Dagobert McChips;1956.08.31.;06700001111;Cooking
Bumfolt Rupor;1967.09.11.;06201112233;Marketing
```

We will search this file with the `grep` command.

### Example 10
Print the lines where the person's skill is IT!

```bash
cat people.csv | grep "IT"
```

With the `cat` command, we read the entire `people.csv` file and use the `|` operator
to "feed" it into the `grep` command. We also specify what we are looking for in the
given line: "IT". `grep` prints the lines that contain the specified text.

### Example 11
Print the lines where the phone number starts with 30.

```bash
cat people.csv | grep "0630"
```

### Example 12
Print the lines where the person was born in 2000 or later.

```bash
cat people.csv | grep ";2"
```

### Example 13
Print the lines where the *Skill* value ends in *-ing*.

```bash
cat people.csv | grep ";*ing"
```

This is already material for the next lesson (regular expressions), but from other
studies we may know that an asterisk generally matches anything.
Unfortunately, we cannot implement much more complicated searches without regular
expressions. We will leave these for the next lesson.

### Example 14
Create a file named `IT.txt` with the following contents:

```
People at IT:
***
```

Replace `***` with the lines of the IT employees from `people.csv`.

```bash
echo "People at IT:" > IT.txt
cat people.csv | grep "IT" >> IT.txt
```

In the lines above, we combined the stream redirection operator with the pipe operator.
We have already seen how they work separately, and using them together is no more
complicated. *We only need to read from left to right to understand what happens.*

## Exercises
Complete the following exercises independently!

### Exercise 1
Collect in a file named `voda.txt` the people from `people.csv` whose phone number starts with 70.

### Exercise 2
Try to enter the folder named `dir_4` and, if successful, create a file named
`im_tired.txt` inside it. If that fails, create the `dir_4` folder!

### Exercise 3
Try to list the contents of the folder named `dir_5`. If that fails, do not print an
error; create the `dir_5` folder instead!

### Exercise 4
Create the following files in a directory named `pictures`:

```
1.jpg
100.jpg
200.jpg
1.png
10.png
20.png
234.jpg
10.svg
```

List the contents of this folder and write every `png` file into a file named
`sprites.list`! (Hint: replace the previously used `cat people.csv` command with
the `ls pictures` command.)

## Summary
We learned the following commands:
- `cat` prints the contents of a file to standard output.
- `echo` prints the text given as an argument to standard output.
- `grep` prints the lines that match the specified pattern.
