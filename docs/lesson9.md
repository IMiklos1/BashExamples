# Lesson 9
In this practice session, we will move away from the technical details of using Bash.
Instead, we will look at some use cases.

It is a basic (and obvious) observation that a language is only as useful as the situations
in which we can use it. Bash was not designed for developing computer games or complex
graphical applications. It works best for system administration, simple command-line automation,
and installation scripts. Since those can become lengthy, I have instead collected a few situations
in which Bash has been useful to me over the years. Most of these will be short.

The use cases below are presented in no particular order.

## Useful examples

### The `ssh` command
I may have started with the most useful one. `ssh` = Secure Shell. We often want to use the
command line on a machine other than the one we are currently using. `ssh` allows remote
command-line access.

```bash
ssh username@jerry.iit.uni-miskolc.hu
```

We can run the command above in a terminal on either a Linux or a Windows machine to connect to
a remote machine. In this example, the remote machine is a server available at
`jerry.iit.uni-miskolc.hu`. Before the `@`, enter the username you want to log in with. The command
then asks whether you accept the fingerprint of the new `ssh` connection (enter *yes*). After that,
enter your password, and the remote connection is ready!

From then on, we can use the terminal as if we were at the remote machine. To disconnect, just
run the `logout` command!

This is useful in the following situations:
- The server is far away, but you need to work on it: maintenance, system administration, or DevOps
tasks.
- You need to connect to the `jerry` server to work on an assignment.
- The server has no graphical interface (which is common). In that case, using `ssh` is easier than
taking a monitor and keyboard to the server room.
- You installed Ubuntu or Raspbian on a small device, typically a Raspberry Pi, and the device is
already inside a robot. You need to connect, but you do not want to carry a monitor and keyboard
along with the robot.

### The `scp` command
Remember the `cp` command? It was used to copy files. `scp` does the same, but stands for *Secure*
Copy. It copies files between machines using SSH. If you connect to a remote machine with `ssh`, you
can do many things *on that machine*, but to copy something from your own machine to it, you need
another command.

The following example copies a file from my machine to my user account on the `jerry` server.

```bash
scp some_file.txt bolyki@jerry.iit.uni-miskolc.hu:~/foldername/
```

As with `ssh`, you will need to enter a password, but after entering it, the file `some_file.txt`
is copied *from this machine* to *that machine*. Put a colon after the other machine's address and
write the file's destination path after the colon. In the example above, the file will be placed in
the `foldername` directory inside my `home` directory.

The `rsync` command serves a similar purpose, although I never learned how to use it.
`scp` is simple and straightforward, and it has been enough for everything I needed.

### `sudo apt install`
As soon as you install your own system, you will encounter this command. This is how it looks on
Ubuntu, although it will be different on Arch Linux. `apt` is Ubuntu's package manager. You can
use it to download and install packages from the official repository. You will encounter it in
almost every installation tutorial, so I will not dwell on it. It is better than next-next-finish.

### The `tar`, `zip`, and `unzip` commands
On a JupyterLab server, I have repeatedly found that I could not download a folder through the
user interface. You may encounter JupyterLab when training neural networks on a remote machine
(a powerful server with substantial graphics cards). JupyterLab can be accessed in a browser and
can run Python code. At the time this document was written, however, it was not possible to download
or upload a folder through it.

Fortunately, the server also has a terminal, so we can issue Bash commands. The solution was to
make a *zip* file from the folder and download or upload that. Extracting it also had to be done
from the terminal rather than by clicking.

By now, I imagine everyone has figured out what the `tar` and `unzip` commands are for.

The name of the `tar` command comes from *Tape ARchive*. It was originally designed for archives
written to tape. It can archive and extract files and folders, with optional compression. It even
has its own file extension (*tar*). Compressed archives commonly use a double extension, as in
`archive_name.tar.gz`. We can compress a folder as follows:

```bash
tar cfzv archive.tar.gz foldername
```

The resulting archive will be named `archive.tar.gz`. We can extract it as follows:

```bash
tar xfzv archive.tar.gz
```

There is much more documentation on `tar`, but this is all I have used it for 95% of the time.
Both `xfzv` and `cfzv` combine four options. Here is what each one means:
- x= eXtract (extract the archive).
- c= Create (create an archive).
- f= File (use the following filename for the archive).
- z= gZip (work with gzip compression).
- v= Verbose (print the files being processed while archiving or extracting).

The `zip` and `unzip` commands are a simpler alternative, which is why I prefer them. If you only
want to create and extract a *zip* file, you could hardly find anything easier.

```bash
# Pack the my_folder directory into a my_archive.zip file
zip -r my_archive.zip my_folder # -r is for recursive
# Extract my_archive.zip
unzip my_archive.zip
```

The `tar` command is a Swiss Army knife for creating, compressing, and extracting archives. `zip`
and `unzip` do just what their names say. They also have options worth reading about if you use
them. However, the example above covers 95% of my use cases.

### The `sl` command
The previous commands took a while to explain, so this one is a breather.
Are you a programmer who likes playing a joke on yourself? The `sl` command was made for you!

What is one of the most frequently typed commands? Yes, `ls`.

What is one of the most common typos? Yes, swapping characters. So, `sl`.

You would not be a real engineer if you did not make a fool of yourself from time to time. The
`sl` command animates a passing train in character graphics whenever you mistype `ls`. You may
need to install it, but it is worth it (someone was kind enough to install it on the department's
machines already).

```bash
sl
```

### The `cowsay` command
A well-known companion to `sl`. It draws a cow in character graphics that says something to you.
After struggling with an assignment for three hours, it will surely have something nice to say.

```bash
cowsay "You suck."
```

### The `history` command
Now let's return to some commands that are genuinely useful. The `history` command has saved my
life a few times. Hopefully, by the end of the semester you have discovered that pressing the up
arrow in the terminal lets you scroll through previous commands. But what if you suddenly need a
command you typed 200 commands ago? It was very long, and you cannot remember its parameter list,
but you know, for example, that it started with `gcc`.

The `history` command comes to the rescue. It prints a list of commands previously issued in the
terminal. Combine it with `grep` to make the search even easier!

```bash
history | grep "gcc"
```

### The `kill` and `ps` commands
You are not a programmer if you have never broken anything. Sometimes you can break something so
badly that you cannot even stop the lousy program you started. Terminal programs can usually be
terminated with `Ctrl+C`. Sometimes, however, you need to kill a program using its PID (Process ID).

First, you need to find the program's PID. This is one of the things `ps` is useful for. Run it
with the following options to see everything!

```bash
ps awux
```

Welcome to Linux process management! You can see all the programs running on the machine. There
are thousands of them. Use `grep` to find your own program.

```bash
ps awux | grep "my_crazy_program"
```

On the far left is the user whose privileges the program runs with. You will surely see your own
username and the `root` user. The next column contains the PID. You can use the PID to forcibly
stop a program.

```bash
kill -9 10234
```

The command above stops the running program (process) with PID 10234. You will learn more about
this in Operating Systems, but I would like to mention something here:

**Note:** The name of the `kill` command is misleading. It is used to send signals to programs,
not just to stop them. Signal 9 is SIGKILL, the signal for **immediate** termination. If we send
a different signal, the program responds differently. For example, pressing `Ctrl+C` also sends
a signal, but a different one: SIGINT (Interrupt). This usually terminates the program too, but
SIGKILL is certain to do so.

**Another note:** It probably goes without saying that indiscriminately killing programs on our
system is likely to cause errors.

### The `gcc` command
Sometimes, especially on Linux and in Operating Systems practice sessions, you need to compile and
run C programs in a terminal. On most Linux systems, the C compiler will be `gcc`.

If you have a single-file C program that you want to compile and run, you can do the following:

```bash
gcc my_prog.c -o my_prog # compile
./my_prog # run
```

Obviously, I do not recommend this method for large programs, but it is enough for small ones.
If you use an external library in your program, you may need to link it:

```bash
gcc my_prog.c -o my_prog -lSDL # compile
./my_prog # run
```

SDL (or rather SDL2) is a useful library for making video games. It is low-level, so I recommend
it to those who want to do some programming.

### The `cloc` command
Are you working on a large personal project or assignment? Have you been writing it for a week and
wondered how much you have written so far? Curious how many lines your code has and how much you
can brag about?

The `cloc` command (Count Lines Of Code) is a small utility that is easy to install on Linux.

```bash
cloc src
```

The command above counts the lines of code in the `src` directory by language.

### Other tools
Most programs, programming languages, and frameworks you will use at university and at work have
some kind of command-line interface (CLI). A typical example is `git`. I like command-line `git`;
others like it less. But the list does not end there. Here are just a few command-line tools I
have had to use:
- `gcc` for C.
- `g++` for C++.
- `java` and `javac` for Java.
- `node`, `npm`, `ng` for web development (Node server, Node package manager, Angular CLI).
- `docker` for managing virtual machines. Here too, I have had to find their IDs and stop them,
or perhaps remove and recreate them. The `grep` command helped a lot.
- `cmake` for building and installing more complex C and C++ projects.

You may or may not encounter these, but it is good to know that the command line does not end
with the Computer Architectures course.

## Commands that may help with the assignment

### The `w` and `who` commands
These provide information about currently logged-in users, their login times, and so on. Some
assignment tasks are based on them.

### Options for the `read` command
Some assignments involve games. Although I would not normally recommend making a game in Bash, the
assignment may be more exciting and enjoyable if you write Pong rather than a tax office simulator.
In a real-time game (one that is not turn-based, like tic-tac-toe), the `-t` option of the `read`
command can be useful. It adds a *timeout* to the command, so the program continues even if you
do not press anything. Just like in a game.

It can also be useful to tell `read` to wait for only one character. Handling one keypress at a
time is enough. If you want to write a game that needs multiple keys at once, you really should
look for another programming language.

You can also silence `read` with the `-s` option (so anything you type will not appear in the
terminal).

Here is an example of its use:

```bash
playing=1
while [[ $playing -eq 1 ]]; do
    read -n1 -t 0.1 -s user_keypress
    if [[ $user_keypress == "w" ]]; then
        echo UP
    elif [[ $user_keypress == "s" ]]; then
        echo DOWN
    elif [[ $user_keypress == "a" ]]; then
        echo LEFT
    elif [[ $user_keypress == "d" ]]; then
        echo RIGHT
    elif [[ $user_keypress == "x" ]]; then
        playing=0 # exit
    fi
    echo "Computer takes action."
done
echo "Game over"
```

In the example above, `read` waits for one character and moves on after 0.1 seconds if no user input
has arrived by then. The `-s` option prevents the input from being displayed in the terminal. You
can use this as a basis for Pong or any other simple character-graphics game.
