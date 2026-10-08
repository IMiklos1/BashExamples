# Lesson 5

So far, we have looked at how to combine the tools available in Bash, but we have not
applied our own algorithms even once. To put this in technical terms, we have so far
focused on the **declarative** use of Bash. However, not everything can be expressed
declaratively, since **imperative** implementations also lie behind declarative language
elements. Inevitably, there will be use cases that are not covered.

The Bash language includes imperative elements for these cases, which we already know
from other courses. It has variables, arrays, if-else conditions, loops (both `for` and
`while`), and functions. However, keep in mind that **a shell scripting language like
Bash works best when we want to express simple things quickly**. It is not intended for
creating complex, carefully planned, finely structured programs.

There is a programming principle called **KISS**, which is especially applicable here.
KISS = "Keep it Simple, Stupid!" If our Bash program starts getting very complicated,
perhaps we should not be writing it in Bash. So use the following constructs wisely;
the fewer we need, the better. Still, they can make life easier from time to time, so
let's get started.

## Variables
The simplest way to declare a variable in Bash is as follows:

```bash
greeting="Hello World!"
```

It does not matter whether the variable already existed. In addition, Bash is a *loosely
typed* or, in other words, *dynamically typed* language. This means that we do not need
to specify the type, since it is determined **at runtime**. This is unlike C and similar
languages, which are *statically typed* and therefore need to know the type **at compile
time**.

If we want to give a variable a special property when creating it, we need to declare it
with the `declare` command and its options. Let's look at the following use cases:

```bash
declare -i num=10 # the -i option declares it as an integer
declare -r constant_thing=10 # the -r option declares it as a read-only variable
# the -a option declares it as an array, but we'll look at that later
# the -x option exports it, but we will hardly need that in this course
```

Declaring a variable as an integer performs some checks and can also raise errors, but
it may produce unexpected behavior for anyone who is not very familiar with the language.

To remove a declared variable, we can use the `unset` command.

```bash
unset num
unset constant_thing # error: it is constant, so we cannot remove it
```

We can also put the output of a command into a variable as follows:

```bash
content=$(ls -la)
```

To access a variable's contents, we need to put a `$` sign before its name. Without it,
the interpreter cannot tell that we want to use the variable's contents rather than the
literal text we typed. The following example shows this:

```bash
greeting="Hi there"
echo greeting # prints "greeting"
echo $greeting # prints "Hi there"
content=$(ls -la)
echo $content # prints what the ls -la command would have printed
```

There are predefined environment variables. For example, `USER` stores the user who
logged into the shell (that is, our username), and `HOME` gives the path to the user's
home directory.

```bash
echo $USER
echo $HOME
```

If we want to see all the environment variables that have been set, we can print them
with the `env` command.

```bash
env
```

The value of a variable can also be easily inserted into text. For example:

```bash
greeting=Hi
echo "Hello $USER!"
echo "$greeting dear friend!"
echo "$greeting $USER! You live at $HOME."
```

If we do not want Bash to interpret variables in the text, we can either put a backslash
before the dollar sign or enclose the text in single quotes. The following two commands
print the same thing:

```bash
greeting=Hi
echo "\$greeting dear friend, \$USER!"
echo '$greeting dear friend, $USER!'
```

If a variable has not been assigned a value, the command interpreter treats it as an
empty string:

```bash
echo "something is $nothing at all."
# Output: something is  at all.
# No error!
```

Since the terminal is also a kind of text interpreter, we will mostly work with text, but
we can perform operations on numbers too. This requires special syntax, though. We may
not like it, but remember: this language was not designed for addition and subtraction;
it was designed for working with files, users, data streams, command-line programs, etc.
If you need more serious arithmetic, choose another language!

The following are all valid:

```bash
a=5
b=7
c=$((a + b))
echo $c
c=$((5 + 9))
echo $c
echo $((80 / 60))
echo $((a-b))
echo $((a*b))
echo $((a%b)) # modulo
```

The dollar sign and double parentheses are used for arithmetic operations. We do not
need to put dollar signs inside the parentheses. Note that Bash uses integer division.
**There is no built-in floating-point type!** We can use a helper program for this, which
we will look at later.

There are other operations for text handling and variable declarations. We will look at
some of them through examples. Here is a short summary:

- `echo` \``ls -la`\` is equivalent to running `echo $(ls -la)`.
- `${variable:-value}` gives the value of `variable` if it is not empty; otherwise, it gives `value`.
- `${variable:=value}` sets `variable` to `value` if `variable` is empty.
- `${variable:?value}` prints `value` and exits with an error if `variable` is empty.
  This is useful for checking that a variable or input parameter is set.
- `${variable:+value}` gives `value` if `variable` is not empty.
- `${#variable}` returns the length of the text in `variable`.
- `${variable:3:5}` returns a substring of the text in `variable`.
- `${variable/"thing"/"stuff"}` replaces the **first** occurrence of "thing" in `variable`
  with "stuff" (and returns the new text).
- `${variable//"thing"/"stuff"}` replaces **all** occurrences of "thing" in `variable`
  with "stuff" (and returns the new text).

## Bash scripts

Any script that creates variables is probably more than one line long. That is why we
want to save these commands in a script file, so we do not have to type them line by line
whenever we want to run our program. Files containing Bash scripts are usually saved with
the `.sh` extension (on Windows, `.bat`, `.cmd`, and `.ps1` are common extensions for
command interpreters). These are plain text files in which we write our commands.

Let's open one in the `nano` text editor in the `lesson5` directory!

```bash
mkdir lesson5
cd lesson5
nano my_script.sh
```

Write the following in it:

```bash
favourite_number=42
echo "The answer to everything is: $favourite_number"
```

Save the file, then return to the terminal! Run it with the following command:

```bash
bash my_script.sh
```

We can also run it as follows (this is also the typical way to run executable command-line files):

```bash
./my_script.sh
```

To do this, however, we first need to write *the path to the command interpreter* on the
first line of the file. Change the file contents to:

```bash
#!/bin/bash

favourite_number=42
echo "The answer to everything is: $favourite_number"
```

This way, the command interpreter will know from the first line of the file how to
interpret the file being run—as a script or executable. This is optional if we do not
intend to run the file this way.

Note: the file also needs execute permission. Set it with the `chmod` command!

## Parameters

Script files are useful for saving our programs and running them later. This lets us
automate certain frequently repeated processes, which can be genuinely useful as
programmers. However, they are not very useful if we cannot pass *parameters* to them.

For example, consider a script that prints all files in a directory provided as a
parameter whose owner is `root`!

Write the following in a file named `useless_printer.sh`!

```bash
#!/bin/bash

echo $(ls -la ~ | grep root)
```

So far, our script is rather useless, since it only works with one directory and one
user. Scripts can, however, be parameterized and invoked as follows:

```bash
bash useless_printer.sh dirname username
```

We have now passed parameters to the script, but the script does not do anything with
them yet. Change it to the following:

```bash
#!/bin/bash

echo $(ls -la $1 | grep $2)
```

In summary:
- `$1`, `$2`, `$3`, etc. provide access to the script's input parameters.
- `$#` gives the number of input parameters.
- `$*` or `$@` gives all command-line parameters together; they can be treated as an array
  and iterated over. We will look at this later.
- `$$` is the process ID of the program running the script.
- `$?` is the return value of the most recently executed command. It can be used for error handling.

We can provide values not only as input parameters, but also read them from the user.
This can be done, for example, with the `read` command:

```bash
read -p "Please type a number! " x

echo "The double of your number is: $((x * 2))"
```



## Examples

### Example 1
Write a simple program that asks the user for a name and phone number, then writes them
to a `cred.json` file in the following format:

```
{
  "name" : "the name written by the user",
  "phone" : "the phone number written by the user"
}
```

The following script is one way to implement this:

```bash
#!/bin/bash

read -p "Please type your name: " name
read -p "Please type your phone number: " phone_num

echo "{" > cred.json
echo -n '  "name" : "' >> cred.json
echo -n "$name" >> cred.json
echo '",' >> cred.json
echo -n '  "phone" : "' >> cred.json
echo -n "$phone_num" >> cred.json
echo '"' >> cred.json
echo '}' >> cred.json
```

### Example 2
Create a shell script file that reads two numbers as input parameters and prints their
- sum,
- difference,
- product,
- quotient,
- remainder
to standard output.

If the input parameters are not provided, both numbers should default to 42.

```bash
#!/bin/bash

a=${1:-42}
b=${2:-42}
echo "$((a + b))"
echo "$((a - b))"
echo "$((a * b))"
echo "$((a / b))"
echo "$((a % b))"
```


### Example 3
Create a shell script file that replaces every occurrence of the word `happy` in a
parameter-provided text file with `not thinking about the exam period`, and saves the
new text in an `out.txt` file.

An example file can be downloaded from the following link:
https://raw.githubusercontent.com/bbalage/BashExamples/master/assets/winnie_the_pooh.txt

```bash
#!/bin/bash

text=$(cat $1)

printf "${text//"happy"/"not thinking about the exam period"}" > out.txt
```

Explanation: we replaced the `echo` command with `printf`. They serve the same purpose,
but `printf` handles line breaks better. Try it with `echo` too, and you will see! In
most cases, it does not matter which one we use.

### Example 4
Write a script that downloads a text file from the following URL, writes the valid email
addresses from it to an `emails.txt` file, and then deletes the originally downloaded file.

URL: https://raw.githubusercontent.com/bbalage/BashExamples/master/assets/file1.txt

```bash
#!/bin/bash

wget https://raw.githubusercontent.com/bbalage/BashExamples/master/assets/file1.txt
cat file1.txt | grep "^[a-Z0-9.]\+@[a-Z0-9.]\+\.[a-Z]\{2,\}" > emails.txt
rm file1.txt
```

Explanation: `wget` is a new command ("web get") that downloads a file from the given
URL. It is one of the more useful commands.

### Example 5
Ask the user for two coordinates. These will be two opposite corners of a rectangle.
The rectangle's sides are parallel to the coordinate axes (if you want to program games,
these are called axis-aligned rectangles).
Print the area of the rectangle! The coordinates **must not** be floating-point numbers.

```bash
#!/bin/bash

read -p "x1: " x1
read -p "y1: " y1
read -p "x2: " x2
read -p "y2: " y2

x_length=$((x2 - x1))
y_length=$((y2 - y1))
area=$((x_length * y_length))

echo "Area: ${area/-/}"
```

There is a little trick at the end that may be unfamiliar to us. Think about it: nothing
in the calculations guarantees that the result will not be negative. But an area cannot
be negative. We ensure this at the end with a string operation.

The `${area/-/}` operation replaces the first `-` found in the `area` variable with
nothing (that is, it removes it).

Remember that Bash is not meant for mathematics? That is quite apparent here. It will
be even more so in the next example. Let's see how to handle floating-point numbers in Bash!

### Example 6
Implement the same thing as in Example 5, but this time the coordinates may be floating-point numbers!

```bash
#!/bin/bash

read -p "x1: " x1
read -p "y1: " y1
read -p "x2: " x2
read -p "y2: " y2

area=$(echo "($x2 - $x1) * ($y2 - $y1)" | bc)

echo "Area: ${area/-/}"
```

`bc` is a helper program (filter) for performing floating-point calculations. It receives
input text that can be interpreted as a sequence of floating-point operations and prints
the result to standard output. We provide input using `echo` and the pipe operator, then
store the output with the `$()` syntax.

The same trick with negative numbers is used at the end.

## Exercises

### Exercise 1
Create a shell script file that generates a configuration file for us in YAML format.
This is an extremely simple format, which in this case will look like this:

```yaml
username: first input
version: second input
site: third input
```

The shell script should prompt the user for the inputs and create the `config.yml` file
accordingly!

### Exercise 2
Use the `people.csv` file from previous lessons. Write a script that replaces the Gmail
email addresses in it with Citromail addresses and writes the new contents to
`strange_people.csv`!

### Exercise 3
Use the `people.csv` file from previous lessons. Write a script that takes the name of
the person to search for as an input parameter and prints their age!
