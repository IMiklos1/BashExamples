# Lesson 4

In this lesson, we will take a closer look at "piping" in Bash
and get an introduction to regular expressions.

Let us prepare our work by creating a folder named `lesson4`,
with the `people.csv` file from the previous lesson inside it
(the easiest way is to copy it).

```bash
mkdir practice/lesson4
cd practice/lesson4
cp ../lesson3/people.csv ./ # copies the file into this directory
                            # under its original name
```

## Chaining pipe operators
Similarly to the `&&` and `||` operators, the `|` (pipe) operator can also
be chained. In general:

```bash
command1 | command2 | command3
```

If we want to read from `people.csv` the people whose phone number starts
with 70 and who were born in the 1900s, we can do so as follows:

```bash
cat people.csv | grep ";0670" | grep ";19"
```

The following happens in this case, illustrated by a diagram:

![filter_commands](img/filter_commands.png)

The `grep` command therefore filtered its input before passing it to its output.
Commands that behave this way are called "filters". **A filter is a command that
reads from input, does something, and then prints the result.** Some examples are:
- `head` prints the first x lines to the output.
- `tail` prints the last x lines to the output.
- `sort` sorts the input and prints it to the output.
- `grep`, which we already know.
- `wc` counts words, characters, and lines, and prints the result to the output.
- `rev` prints the input in reverse, line by line.
- `uniq` examines counts and uniqueness in sorted data (for example, it checks how
many times a line occurs).
- `cut` extracts columns from the data.
- `tr` replaces characters in the data.

Let us look at some of them through examples!

### Example 1
Print the last three lines of the `people.csv` file!

```bash
cat people.csv | tail --lines=3
# The following command does the same thing
# (we do not need to write '--lines')
cat people.csv | tail -3
```

The `tail` command works as a filter like `grep`, but performs a different operation.
It prints the number of lines specified after the command name from the end of the file.

### Example 2
Print the first three lines of the `people.csv` file, including the header!

```bash
cat people.csv | head --lines=3
```

`head` works like `tail`, except that it prints the first lines rather than the last ones.

### Example 3
Print the last line in the `people.csv` file where the phone number starts with 30!

```bash
cat people.csv | grep ";0630" | tail -1
# Or:
cat people.csv | grep ";0630" | tail --lines=1
```

The command above is easy to understand based on what we have learned so far,
once we know that pipes can be chained across many commands.

### Example 4
Print all lines of the `people.csv` file except the last five!

```bash
cat people.csv | head --lines=-4
# The following does the same thing
cat people.csv | head
```

If we specify a negative number after `--lines`, it does not print the first *n* items;
instead, it **does not** print the last *n* items. `tail` works similarly, with a small
twist that we will see in the next example.

### Example 5
Print the first three lines of the `people.csv` file without the header!

```bash
cat people.csv | tail --lines=+2 | head --lines=3
# Or more briefly:
cat people.csv | tail +2 | head -3
```

In this case, `tail` starts printing at the *n*th line, where the number is specified
with a `+` sign (not a minus sign, as with `head`).

### Example 6
Print the first 10 files or directories in `/dev`, even if they are hidden!
The special `.` and `..` entries should not be displayed!

```bash
ls -la /dev | tail +4 | head -10
```

Here, we removed the first three lines of the `ls -la` command (the *total number of disk
blocks* line and the two special directory lines), then kept the first 10 of the remainder.

This example is closer to how we use Bash in practice. It is easy to demonstrate
these commands on a CSV file, but in practice we work not only with file contents,
but also with command output. This usually comes up when we use a command-line tool
that lists something for us. We do not really know such tools yet, and will not learn
them until we encounter them in a real or university project.

Nevertheless, let us mention the `docker` tool as an example. It manages virtual
machines and, with the appropriate options, prints the currently running virtual
machines with their names and identifiers. The `grep` command is useful for finding
the correctly named machine among many virtual machines. If we are real command-line
mages, we can also extract the ID next to the name using Bash and even stop the machine.
This would involve tedious searching, selecting, copying, and pasting, but if we write
a script for it, it can be done with a single command.

**One of Bash's major advantages is that we can automate frequently repeated command-line
commands.**

### Example 7
Print the last 5 disks in the `/dev` folder (for now, consider files whose owner group
is `disk` to be disks)!

```bash
ls -la /dev | grep " disk " | tail -5
```

### Example 8
Print only the people's names from the `people.csv` file (without the header)!

```bash
cat people.csv | tail +2 | cut --field=1 --delimiter=';'
# Or the same thing more briefly
cat people.csv | tail +2 | cut -f 1 -d ';'
```

The last element of the command above, the `cut` command, requires some explanation.
It reads the appropriate columns from text consisting of columns. A column can be
specified by bytes or characters, but we need *fields* here. The `--fields=1` option
tells it which field to read from the input text. Within the file, however, fields
must be separated by something, which is generally called a *delimiter*.
The `--delimiter=';'` option specifies which character separates the columns in the
input text (in this case, the semicolon).

### Example 9
Print only the phone numbers from the `people.csv` file, and only the first one beginning with 20!

```bash
cat people.csv | grep ";0620" | cut -f 3 -d ';' | head -1
```

### Example 10
Print the people's names from the `people.csv` file in alphabetical order (names only).

```bash
cat people.csv | tail +2 | cut -f 1 -d ';' | sort
```

### Example 11
Print the birth months from the `people.csv` file (months only)!

```bash
cat people.csv | cut -f 2 -d ';' | cut -f 2 -d '.'
```

At this point, we can begin to see that we can go quite far by combining these
commands. There are many commands with their own options, and they work similarly
to those we have seen so far. Instead of learning and combining more commands,
let us move on to regular expressions, which will also be useful to us!

## Regular expressions
One of my teachers once told me at university: "The two hardest things in computer
science are caching and regular expressions."

Regular expressions are used for *text matching*. **A regular expression is a pattern
that a "regular expression engine" can use to unambiguously determine whether the
expression matches a given piece of text.** It is easier to understand with examples:
the following are regular expressions, followed by what they match:

- `[0-9]{3}`: matches every sequence of three digits.
- `^[A-Z][a-z]+`: matches every word beginning with an uppercase letter.

Next, we will learn about some building blocks of regular expressions.
This document provides a short summary, but the topic is much broader, and the
[Internet](https://www.regular-expressions.info/) contains a great deal of information
about it. Regular expressions are widely used, for example, when validating a web form
(to specify what structure of text a field may contain) or when searching (to specify
what structure of text to look for). It is a useful but complicated topic, and we will
not explore it in great depth.

We will continue practicing on the contents of `people.csv`, but I suggest that we
leave the terminal for a while at this point, because it will only cause us endless suffering.

There is one more thing I would like to point out about Bash commands:
we have seen that I used both types of quotation marks during the lesson, single quotes
(`'`) and double quotes (`"`). This will have practical significance from now on,
because single quotes contain **only text**, while double quotes **can contain variables
that need to be interpreted**. We will examine this in more detail in the next lesson;
for now, it is enough to remember that **regular expressions should preferably be
written inside single quotes**!

Give the `people.csv` file new contents to make the task more complex and justify
using regular expressions! The new contents are:

```
Name;Birthdate;Phone;Email;Skill
Robert Bob;1997.09.12.;06201975555;bob2bob@gmail.com;IT
Zsuber Driver;1988.10.11.;06404445555;uber@gmail.com;Driving
Hatori Hanso;1966.01.11.;063012345;jean'dark@gmail.com;Smithing
Rinaldo Orson;2001.05.24.;06709330000;D&D@gmail.com;IT,Weight lifting
Travis Rick Camel;1970.10.01.;06301717171;mummy@citromail.hu;Horses
Dagobert McChips;1956.08.31.;067001111;clap.clapgmail.com;Cooking,Catery
Bumfolt Rupor;1967.09.11.;06201112233;HP123@mg.mh.hu;Marketing
Mad Marx;1965.09.11.;06301442233;money.bad@gmail.com;Motorcycle Riding
Borland Ci Rattleneck;1980.11.02;+36304225555;teller@citromail.hu;Bottleneck Construction
Baileys Margareti;1999.12.09;06209876543;b1gdr1nk@HOND.hu;Drinking,Catering
Larry Lipstick;1988.07.22;+36206547382;baby@gmail.com;Hairstyling
Inigo Montoya;1977.04.11;+367065682;pr3pare@gmail.to;Swordfighting
```

The https://regex101.com/ website lets us try regular expressions interactively and
in a relatively visual way. The following screenshot illustrates what we should see
on the website. Copy the contents of `people.csv` into the location shown in the image
(the largest text box in the center).

![regex](img/regex_screenshot.png)

The top-right corner contains explanations in English of the meaning of the expressions
we enter. We can enter regular expressions near the top center to search the text.
The bottom-right corner contains a few short reminders about the most common regular
expressions. I will list some of them here, and then we will look at them through examples!

- The `.` matches any single character.
- The `?` character specifies that the preceding character may occur once or zero times
(so the expression `blabla?` matches `blabla` or `blabl`).
- The `*` character specifies that the preceding character may occur any number of times,
including zero (so `blabla*` matches `blabla`, `blablaaaaaaaa`, or `blabl`).
- The `+` sign means one or more repetitions of the preceding character (so `blabla+`
matches `blabla` or `blablaaaaa`, but **does not** match `blabl`).
- `[]` matches one of the characters listed inside the square brackets (for example,
`[abc]`) or one character from a range (for example, `[a-z]`).
- `[^]` does **not** match the characters listed inside the square brackets (the negation
of the previous form); it matches any other single character.
- `{,}` specifies numerically how many times the preceding character may occur. For example,
`{2,4}` means at least 2 and at most 4 occurrences. `{N,}` matches N or more characters.
`{N}` matches exactly N characters. Note that `?`, `*`, and `+` can also be specified
with brace intervals: `{0,1}` = `?`, `{0,}` = `*`, and `{1,}` = `+`.
- The `^` character matches the beginning of a line.
- The `$` sign matches the end of a line.

These are the most commonly used building blocks of regular expressions. We will not need
anything else in this course.

We will use regular expressions with the `grep` command. To make `grep` interpret regular
expressions, we must provide the `-E` option (as in `Expression`), followed preferably by
the regular expression inside single quotes. Before giving the expression to `grep`, make
sure on the website above that the expression is correct!

### Example 12
Print the people from the `people.csv` file whose first name starts with R!

```bash
cat people.csv | grep -E '^R'
```

The `^` sign denotes the beginning of a line, so we searched for lines that begin with R.

### Example 13
Print the people from the `people.csv` file whose last name starts with R!

```bash
cat people.csv | grep -E '^[a-zA-Z ]+R[a-z]+;'
```

The expression above matches every uppercase R that is at the beginning of a word before
the first semicolon, with only letters and spaces preceding it.

### Example 14
Print the people from the `people.csv` file who have at least two skills!

```bash
cat people.csv | grep -E ',[a-zA-Z ]+$'
```

We searched for a comma before the words at the end of the line.

### Example 15
Print the people from the `people.csv` file who have three names!

```bash
cat people.csv | grep -E '^[a-zA-Z]+ [a-zA-Z]+ [a-zA-Z]+;'
```

### Example 16
Print the people from the `people.csv` file whose email address is valid!
(For now, a valid email address is one whose username contains only letters, digits, and
`.` characters, followed by `@` and a lowercase group name, separated by `.` from a final
label of at least 2 and at most 4 characters; the username must be between 3 and 100
characters, and the group name between 3 and 20 characters.)

```bash
cat people.csv | grep -E ';[0-zA-Z0-9.]{3,100}@[a-z]{3,20}\.[a-z]{2,4};'
```

A text above draws attention to using the *backslash* character (`\`) before the `.`
character. The `.` character has a special meaning by default, so if we want to use it as
a literal character, we must write a *backslash* before it. The same applies to the other
special characters as well (`?`, `+`, `{`, `}`, `\`, etc.).

## Notes
Earlier, we used the `cat` command to list the contents of a file. This was useful because
we learned the command and demonstrated piping, but it can actually be omitted in the
examples above.
The file name to search can be provided as the last parameter of the `grep` command.
The same is true for the other filter commands we have seen. For example, the following
is valid:

```bash
head -1 people.csv
```

However, this method cannot be used to process the output of the `ls` command, for example
(although later we will write a loop to iterate over the contents of a directory).
Therefore, the following will not work:

```bash
head -1 ls
```

## Exercises

Let us look at some independent practice exercises! The contents of the `people.csv` file
should be the new, updated contents.

### Exercise 1
Print, in alphabetical order by name, the people who have only one skill!

### Exercise 2
Print the names and phone numbers of the people whose one of their names ends in o!

### Exercise 3
Print the first names of the people whose phone number starts with +36 and is valid
(9 digits follow +36, and the number starts with 20, 30, or 70)!

### Exercise 4
Print the last names of the people whose phone number starts with 06 and is valid!

### Exercise 5
Print the names of the people who were born on an even-numbered day!
