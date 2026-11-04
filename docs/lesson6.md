# Lesson 6

Bash also has conditional statements. We will look at these in this lesson.

Bash has a multitude of conditional operators. A summary is shown below.
After the list, we will look at how to use each operator.

## Conditional operators
### Integers
Let `x` and `y` be integers.

```bash
x=10; y=20
```
| Operation      | Bash | Meaning |
| ----------- | ----------- | - |
| x < y | `$x -lt $y`| less-than|
| x <= y   | `$x -le $y` | less-or-equal |
| x > y   | `$x -gt $y` | greater-than |
| x >= y   | `$x -ge $y` | greater-or-equal|
| x == y   | `$x -eq $y` | equal |
| x != y   | `$x -ne $y` | not-equal|

### Strings
Let `text1` and `text2` be strings.

```bash
text1=blabla
text2="Bla bla"
```

| Bash | Meaning |
| ----------- | - |
| `$text1 == $text2`| equality|
| `$text1 != $text2`| inequality|
| `$text1 < $text2`| text1 sorts before text2 (according to the current *locale*)|
| `$text1 > $text2`| text1 sorts after text2 (according to the current *locale*)|
| `-n $text1`| text1 is not empty |
| `-z $text1`| text1 is empty |
| `$text1 =~ "regex_pattern" `| text1 matches the regex_pattern regular expression |

### Files
Let `file_path` be the path to a file, and `other_file_path` the path to another file.

| Test | Bash | Meaning |
| -- | ----------- | - |
| Does the file exist? | `-e file_path`| exists |
| Does the directory exist? | `-d file_path`| directory |
| Has it been modified since it was last read? | `-N file_path`| new |
| Is it owned by the current user? | `-O file_path`| owned |
| Is it readable? | `-r file_path`| readable |
| Is it writable? | `-w file_path`| writeable |
| Is it executable? | `-x file_path`| executable |
| Is it non-empty? | `-s file_path`| size greater than zero byte |
| Is one file newer than the other? | `file_path -nt other_file_path`| newer than |
| Is one file older than the other? | `file_path -ot other_file_path`| older than |
| Are they the same file (on the filesystem)? | `file_path -ef other_file_path`| equal file |

---
Now that we know the test operators, let's look at the syntax we can use to actually
apply them!

## && and || constructs
We have seen these before. They still do the same thing as before, but we can use the `test` command
to associate a condition with them. For example:

```bash
x=10; y=20
test $x -lt $y && echo "Smaller" # -lt means less-than
test $x -gt $y && echo "Greater" # -gt means greater-than
```

This way of specifying conditions works well for one-liners. For example, the following code
creates a file if it does not exist.

```bash
test -e some_file.txt || touch some_file.txt # -e means exists
```

There are also two utilities on the system, `true` and `false`. As their names suggest,
they behave as if we had put a condition that always evaluates to true (`true`) or always evaluates
to false (`false`) in their place. For example:

```bash
true && echo "This is written"
true || echo "This is NOT written"
false && echo "This is NOT written"
false || echo "This is written"
```

The `test` command also has a *shortcut* in the form of square brackets. The following two
command lines are equivalent.

```bash
test 1 -ne 2 && echo "Thing"
[ 1 -ne 2 ] && echo "Same thing"
```

This will be more intuitive with the `if` construct. We will also use the
`[[ condition ]]` construct, with double square brackets, in `if` statements.

```bash
[[ 1 -ne 2 ]] && echo "Almost the same thing"
```

This is almost the same as single square brackets (and therefore the `test` command),
except that it allows more forms of syntax. For example, only here can we use the
regular expression comparison operator `=~`.

From now on, we will mostly use the `if-else` construct because it is more flexible.

## if-else construct
When writing `if` constructs, we can use the `test` operator as before:

```bash
if test 1 -eq 1; then
  echo "true"
fi
```

However, the following is more intuitive to read (especially if you know imperative languages):

```bash
if [ 1 -eq 1 ]; then
  echo "true"
fi
```

The two expressions above mean the same thing. From now on, we will mostly use the expression
below because it allows us to express a wider range of conditions:

```bash
if [[ 1 -eq 1 ]]; then
  echo "true"
fi
```

For this simple example, of course, all three mean the same thing.

Unlike in other languages, the Bash `if` construct ends with `fi`,
which is `if` backwards. We will see this logic elsewhere too.

We can also add an `else` branch to an `if` construct:

```bash
if [[ 1 -ne 1 ]]; then
  echo "true"
else
  echo "false"
fi
```

Bash implements the `else-if` construct as follows:

```bash
x=10
if [[ $x -eq 1 ]]; then
  echo "equal"
elif [[ $x -lt 1 ]]; then
  echo "less than"
else
  echo "greater than"
fi
```

As you can see, the words `else-if` have been combined into `elif`.

The `switch-case` construct known from C is also available here, with the following syntax:

```bash
read -p "Which type of person are you, 0, 1, 2 or 3" ptype
case $ptype in
0)
  echo "You are chaotic evil.";;
1)
  echo "You are a mermaid.";;
2)
  echo "You are phylosopher.";;
3)
  echo "You are a Tauren Druid.";;
*)
  echo "That's not a person type, it's a default!";;
esac
```

Within the `[[]]` construct, we can use the logical operators AND, OR and NOT similarly to C.
For example, the following checks whether x is between 10 and 20:

```bash
if [[ $x -gt 10 && $x -lt 20 ]]; then 
  echo "OK"
fi
```

Or whether it is NOT a number:

```bash
reg_ex='^[0-9]+$'
# The exclamation mark negates the following expression
if [[ ! $x =~ $reg_ex ]]; then
  echo "Not a number."
fi
```

Now we know roughly everything we need for our conditional code.
Let's see some examples!

## Examples
Input validation will be common in the following examples. If an input is invalid,
we exit the program with an error. The program's return value can signal an error. If the
return value is nonzero, it indicates an error. We can exit early with the `exit` command.

For example:

```bash
#!/bin/bash

if [ 1 -ne 1 ]; # very unexpected error
  exit 1 
fi
```

If we do not write a number after `exit` (or do not write an `exit` command in the program), the return
value will automatically be zero (even if a command in the program produced an error).
For example, the following program does not return an error, although `grep` itself fails.

```bash
#!/bin/bash

grep "hello" nonexistent_file_blabla.txt
```

### Example 1
Write a shell script that takes the lengths of two sides of a rectangle as input and prints
its area! Implement it using only integers! (Of course, validate the
input!)

```bash
#!/bin/bash

if [[ $# -ne 2 ]]; then
  echo "Two inputs are needed, $# is given." 1>&2
  exit 1
fi

reg_ex='^[0-9]+$'
if [[ ! $1 =~ $reg_ex || ! $2 =~ $reg_ex ]]; then
  echo "Both inputs must be positive integer numbers!" 1>&2
  exit 1
fi

echo "Area is: $(($1 * $2))"
```

The `1>&2` expression after `echo` redirects the standard output stream to the standard error stream.

The first `if` expression checks whether exactly two inputs were provided.

The second `if` expression ensures that if the first or the second expression does not match our
regular expression for integers, we exit with an error message.

### Example 2
Suppose we have a file named `name_id_pairs.txt` containing name and ID pairs.
Write a shell script that asks for a name and prints the corresponding ID,
or reports an error if the name is not found in the file.

The file should contain the following:

```
zsuzso:aef7421b
alfonz:eef4555b
barnabas:98726371
tom:aaaabbbb
jerry:cccdddee
hasselhoff:cafebabe
```

The following script implements the task:

```bash
#!/bin/bash

read -p "Please, type the name you are searching for! " name

row=$(cat name_id_pairs.txt | grep $name)

if [[ -z $row ]]; then
  echo "No name like that." 1>&2
  exit 1
fi

id=$(echo $row | cut -d ':' -f 2)
echo $id
```

### Example 3
MVK Zrt. makes a standard GTFS database available to developers so they can integrate timetable
data into their own applications. Write a shell script that downloads this
database and lists the routes that depart from or go to Centrum!

**Commands:**
wget, unzip (for extraction), cat, grep

**Required checks:**
- If the file to be downloaded has already been downloaded, delete the previous
version before downloading it again!
- If the downloaded archive has previously been extracted into a directory, remove that directory's
contents before extracting it again!

**Note:** In the past, the database could not be downloaded from the department's computers due to
*certificate* issues, so I also uploaded it to a *github* repository. The corresponding command is
commented out. If an error occurs, replace the current URL with the following:
*https://raw.githubusercontent.com/imiklos1/BashExamples/master/assets/mvkzrt.zip*

```bash
#!/bin/bash

if [ -e gtfs.zip ]; then
    rm mvkzrt.zip
fi

if [ -d gtfs ]; then
    rm -r mvkzrt
fi

wget "https://mvkzrt.hu/gtfs/mvkzrt.zip"
unzip mvkzrt.zip -d mvkzrt # try it without the -d option too...
cat mvkzrt/routes.txt | grep "Centrum"
```

### Example 4

Write a shell script that asks for the user's date of birth in `yyyy.mm.dd` format!
Check that the date is valid and print the user's age!

Use the `date` command to get the current date!

```bash
#!/bin/bash

read -p "Please, type your birthdate in yyyy.mm.dd format! " bdate

reg_ex='^[0-9]{4}\.[0-9]{2}\.[0-9]{2}$'
if [[ ! $bdate =~ $reg_ex ]]; then
  echo "Date is not in proper format!" 1>&2
  exit 1
fi

byear=$(echo $bdate | cut -f 1 -d '.')
bmonth=$(echo $bdate | cut -f 2 -d '.')
bday=$(echo $bdate | cut -f 3 -d '.')

# The date command can check the validity of a syntactically correct
# date. For example, whether the month is greater than 12, etc.
date -d "$byear-$bmonth-$bday" > /dev/null || exit 1

bseconds=$(date -d "$byear-$bmonth-$bday" +%s)
cseconds=$(date +%s)

age_in_seconds=$((cseconds - bseconds))

if [[ $age_in_seconds -lt 0 ]]; then
  echo "You haven't been born yet!" 1>&2
  exit 1
fi

echo $(( age_in_seconds / 60 / 60 / 24 / 365 ))
```

## Exercises

### Exercise 1
Implement the task from *Example 1*, but this time using floating-point numbers!

### Exercise 2
Implement the task from *Example 2*, but this time make it work not only with a file named
`name_id_pairs.txt`, but with a file of any name! The script should accept the file name as an
input parameter! Check that the file exists and is readable before implementing the rest of the
functionality!

### Exercise 3
Implement the task from *Example 3*, but this time accept any terminus instead of Centrum
and pass it to the script as an input parameter. If there is no such terminus,
print an error message!
