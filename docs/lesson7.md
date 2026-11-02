# Lesson 7
In the seventh practice session, we will cover loops and arrays.

## Loops
Bash has `for` and `while` loops, which you are surely familiar with from elsewhere, with slightly different syntax.
There is also an `until` loop, which is not very different from a `while` loop, except
that it runs while the condition is false rather than while it is true.

Let's look at some examples of loops!

### Example 1
Print the numbers from 0 to 10 using `for`, `while` and `until` loops!

```bash
#!/bin/bash

# Using a for loop
for (( i = 0; i <= 10; i++)); do
    printf "$i "
done
echo

# Using a while loop
i=0
while [ $i -le 10 ]; do
    printf "$i "
    i=$((i + 1))
done
echo

# Using an until loop
i=0
until [ $i -gt 10 ]; do
    printf "$i "
    i=$((i + 1))
done
echo
```

### Example 2
Generate a random number and print the square numbers smaller than it!

Random number generation in Bash is available through the RANDOM variable. Below are some examples
of generating numbers in specified intervals.

```bash
echo $((RANDOM % 100)) # [0-99]
echo $((RANDOM % 101)) # [0-100]
echo $((RANDOM % 100 + 1)) # [1-100]
echo $((RANDOM % 50)) # [0-49]
echo $((RANDOM % 51 + 50)) # [50-100]
```

The following script is one solution to the task:

```bash
#!/bin/bash

rnd=$RANDOM

echo "The number is: $rnd"
echo "Lesser square numbers are: "

for (( i=1; i*i < $rnd; i++ )); do
  echo -n "$((i*i)) "
done
```

### Example 3
Ask for a value of `N` and print the first `N` Fibonacci numbers, taking zero as the
zeroth Fibonacci number. Check that `N` is an integer with a value of at least 2!

```bash
read -p "Please, give the value of N (at least 2):" N

reg_ex='^[0-9]+$'
if [[ ! $N =~ $reg_ex ]]; then
    echo "N must be a number!" 1>&2
    exit 1
fi

if [ N -lt 2 ]; then
    echo "N was too small." 1>&2
    exit 1
fi

f_before_prev=0
f_prev=1
echo "0: $f_before_prev"
echo "1: $f_prev"
for ((i = 2; i <= N; i++)); do
    f=$((f_prev + f_before_prev))
    echo "$i: $f"
    f_before_prev=$f_prev
    f_prev=$f
done
```

### Example 4
Write a shell script that expects a single positive number as an input parameter (and reports an error if it does not
receive one). The program should print whether the given number is prime.

```bash
if [ $# -lt 1 ]; then 
    echo "No number provided as input." 1>&2
    exit 1
fi

num=$1
re='^[0-9]+$'
if ! [[ $num =~ $re ]] ; then
    echo "error: Not a positive number." 1>&2;
    exit 1
fi

if [ $num -le 1 ]; then
    echo "Not prime."
    exit 0
fi

square_root=$(echo "scale=0; sqrt($num)" | bc)

for ((i=2;i<=square_root;++i)); do
    if [ $((num % i)) -eq 0 ]; then
        echo "Not prime."
        exit 0
    fi
done

echo "Prime."
```

## Arrays
Arrays in Bash are more flexible than in a C-like language. When an array is created, we do not need to know
its length. In practice, arrays work more like lists, as we will see in the following
code examples.

We can declare an array as follows:

```bash
declare -a arr
```

We can assign values to the array elements as follows. The order is not fixed, and we can skip elements.
In that case, the skipped elements will be empty.

```bash
arr[0]=12
arr[1]=3
arr[5]=6
```

To access an element of the array, we can use the following syntax:

```bash
echo ${arr[1]} # the second element
```

We can declare and initialize an array at the same time. The following code creates an array and
prints its contents:

```bash
declare -a arr=(12 3 6)

# ${#arr[*]} gives us the number of array elements
for ((i=0;i<${#arr[*]};++i)); do 
    echo ${arr[i]}
done
```

The `for` loop also has another useful syntax, commonly called a `for-each` loop,
which is found in most imperative languages.

```bash
declare -a arr=(12 3 6)

# ${arr[*]} returns all elements of the array
for element in ${arr[*]}; do 
    echo $element
done
```

Interestingly, we can obtain the list of the program's input parameters, and even the output of a command,
as an array. This makes it easy to iterate over them with a `for-each` loop. The following examples deal with arrays.

### Example 5
Print all arguments received by the program!

```bash
for i in $*; do
    echo $i
done
```

### Example 6
List the contents of a directory, numbering each file and directory!
The directory path is provided as an input parameter. Check that we received an input
parameter and that it is an existing directory!

```bash
#!/bin/bash

# Here we check whether the variable is empty
if [[ -z $1 ]]; then
    echo "No input given." 1>&2
    exit 1
fi

if [[ ! -d $1 ]]; then
    echo "Not an actual directory." 1>&2
    exit 1
fi

i=0
for name in $(ls $1); do
    echo "$i: $name"
    i=$((i+1))
done
```


### Example code 7
Fill an array of 10 elements with random numbers, then iterate over the numbers and increase each one
by 1!

```bash
#!/bin/bash

declare -a arr

for ((i=0; i<10; ++i)); do
    # random number from 0 to 99
    arr[$i]=$((RANDOM % 100))
    printf "${arr[$i]} "
done
echo

for ((i=0; i<10; ++i)); do
    arr[$i]=$((arr[i] + 1))
done

for element in ${arr[*]}; do
    printf "$element "
done
echo
```

### Example 8
Create an array of N elements, asking for N with the `read` command and checking that it is
a positive integer (returning an error if it is not). Fill the array with random numbers
from the interval \[1-100\]! Then perform the following
operations on the array:
- Print the minimum element (its index and value).
- Print the maximum element (its index and value).
- Print the sum of the elements.
- Print the average of the elements.


```bash
read -p "Please type the size of the array!" N

re='^[0-9]+$'
if ! [[ $N =~ $re ]] ; then
    echo "error: Not a positive number." 1>&2;
    exit 1
fi

if [ $N -eq 0 ]; then
    echo "error: Array size is 0." 1>&2
    exit 1
fi

declare -a arr

for ((i=0; i<N; ++i)); do
    # random number from 1 to 100
    arr[$i]=$((RANDOM % 100 + 1))
    printf "${arr[$i]} "
done
echo

sum=arr[0]
mini=0
maxi=0
for ((i=1; i<N; ++i)); do
    if [ ${arr[$i]} -gt ${arr[$maxi]} ]; then
        maxi=$i
    fi
    if [ ${arr[$i]} -lt ${arr[$mini]} ]; then
        mini=$i
    fi
    sum=$((sum + arr[i]))
done

echo "Minimum: $mini - ${arr[$mini]}."
echo "Maximum: $maxi - ${arr[$maxi]}."
echo "Sum: $sum."
avg=$(echo "scale=4; $sum / $N" | bc)
echo "Average: $avg."
```

## Exercises
Independent exercises for practice.

### Exercise 1
Ask the user for a positive integer, then print the odd positive
integers that are smaller than that number!

### Exercise 2
Implement the task from Example 6, but this time allow the script to accept any number of input
directories. Check that we received at least one input parameter and that
every input parameter is a directory. If so, print the contents of each one! Do not interrupt the
numbering!

### Exercise 3
Write a script that downloads the following .zip file from the internet:
*https://raw.githubusercontent.com/bbalage/BashExamples/master/assets/nums.zip*


The script should extract the directory, then copy all `.txt` files from it into an
`out_nums` directory, dividing each line in the `.txt` files that contains a number
by two!
