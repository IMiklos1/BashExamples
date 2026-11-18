# Lesson 8

In the eighth practice session, we will cover functions.

## Functions
Functions in Bash do not work quite the same way as in C or other imperative languages.
They are still organizational units that we can call multiple times in a program or use to make
our code more readable. Besides the syntax, the difference lies in how return values are handled.

It is most practical to think of Bash functions as scripts or utilities themselves, such as
`cut`, `grep`, `cat`, etc. While in C a return value is mostly used to return a computed value,
here it indicates success or failure.

The easiest way to understand this is through examples.

In Bash, we can define and call a function as follows:

```bash
f() {
    echo "Hello world!"
}

f
```

The following syntax is also allowed (it is a matter of preference):

```bash
function f {
    echo "Hello world!"
}

f
# Prints Hello world! 
```

Think of the function as a small command! We also pass arguments to it accordingly:

```bash
function add_two_numbers {
    echo "$(($1 + $2))"
}

add_two_numbers 10 30
# Prints 40
```

We can store its output in a variable, just as with a command.

```bash
function add_two_numbers {
    echo "$(($1 + $2))"
}

sum=$(add_two_numbers 10 30)
add_two_numbers $sum 15
# Prints 55
```

Its return value is not meant to return the result, but to indicate an error.
For example:

```bash
function add_two_numbers {
    regex="^[0-9]+$"
    if [[ ! $1 =~ $regex || ! $2 =~ $regex ]]; then
        echo "Must be two numbers!" 1>&2
        return 1 # We use "return", not "exit".
                 # "exit" would quit the entire script
    fi
    echo "$(($1 + $2))"
    # The default return value is 0
}

add_two_numbers 10 20 && echo "OK" || echo "Not OK" # This succeeds
add_two_numbers 10 e0 && echo "OK" || echo "Not OK" # This fails
```

We can also obtain the return value using `$?`, which always gives the return value of the most
recent command or function call:

```bash
function add_two_numbers {
    regex="^[0-9]+$"
    if [[ ! $1 =~ $regex || ! $2 =~ $regex ]]; then
        echo "Must be two numbers!" 1>&2
        return 1
    fi
    echo "$(($1 + $2))"
}

add_two_numbers 10 20
echo $? # Prints 0
add_two_numbers 10 e0
echo $? # Prints 1
```

Notice that we use `return` instead of `exit`, but otherwise everything is the same as when writing
a script. We can therefore apply what we learned in previous lessons.

IMPORTANT: variable scope differs between scripts and functions.
We can observe this by running the following example:

```bash
x=10
f() {
    x=$((x+1))
    echo $x
}

f
f
f
```

The code above prints 11, 12, and 13. In C terminology, we could call the variable `x` global.
If we do not expect this, it can lead to unexpected errors.

We can define a local variable that shadows another variable using the `declare` keyword:

```bash
x=10
f() {
    declare x
    x=10
    x=$((x+1))
    echo $x
}

f
echo $x
# Prints 11, then 10
```

However, we need to be careful when creating variables. In the following case, `y` will not exist
outside the function:

```bash
x=10
f() {
    declare y
    y=10
    y=$((y+1))
    echo $y
}

f
echo $x
echo $y
# y is unset by the end
```

In the following case, however, it will exist:

```bash
x=10
f() {
    y=10
    y=$((y+1))
    echo $y
}

f
echo $x
echo $y
# y exists at the end, and it's 11
```

Tricky, isn't it? The following code illustrates what is happening:

```bash
x=10
f() {
    local y=11
    z=12
}

f
echo $x
echo $y
echo $z
# y is unset; z exists and is 12
```

The variable `y` is local, while `z` is not, so `y` is not visible outside the function, but `z` is.
The `declare` keyword creates local variables inside a function.
In the following code there will be two variables named `x`, one inside and one outside the function
(local and global):

```bash
x=10
f() {
    local x=11
    echo $x
}

f
echo $x
# Prints 11 and 10
```

Now that we have seen how to use functions, let's look at some practical examples!

## Examples

### Example 1
Create a function that adds the numbers passed as arguments. If it receives no arguments, it should
print 0; otherwise, it should print their sum. There is no need to check whether the arguments are
numbers.

```bash
#!/bin/bash

sum() {
    local x=0
    for i in $*; do
        x=$((x + i))
    done
    echo $((x))
}

sum 10 20 30
sum
```

### Example 2
Write a function that generates `N` random numbers in the range [`x`-`y`]. The function receives
`N`, `x`, and `y` as parameters.

No validation is required inside the function. If any parameter is omitted, its default value should
be as follows: `N=5, x=1, y=90`.

Use the function to generate 10 random numbers between 800 and 900, then 15 numbers between -10 and 10!

```bash
#!/bin/bash

generate() {
    local N=${1:-5}
    local x=${2:-1}
    local y=${3:-90}
    range_size=$((y - x + 1))
    declare -a arr
    for ((i=0; i < N; ++i)); do
        rnd=$((RANDOM % range_size + x))
        arr[$i]=$rnd
    done
    echo ${arr[*]}
}

generate 10 800 900
generate 15 -10 10
```

## Exercises

Exercises to solve independently.

### Exercise 1
Extend the second example so that the generated numbers are unique, and generate winning numbers
for a five-number lottery!

### Exercise 2
Create a function that takes a vector and prints its length. The dimension of the vector is the
number of input parameters. If it receives no input parameters, it should report an error!

Write a script that uses this function! Ask the user for a vector and calculate its unit vector
(the direction vector of length one, obtained by dividing each component by the vector's length).
