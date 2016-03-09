## `pdb` Tutorial

The purpose of this tutorial is to teach you the basics of `pdb` (the Python DeBugger) and some helpful tricks to make your
debugging sessions a lot less stressful. The tutorial works best if you use Python 2.7 or Python 3.4 and I
will highlight the differences between the two versions if a `pdb` command differs. To check what version of python you're
using, type the following in your terminal:

```shell
python --version
```

Now that you know your version, let's get to it!


### What is the purpose of a debugger?

Before jumping into the code, we should have a brief discussion about the importance of debugging and using
a debugging tool. For me, these three points highlight the importance of a debugger.

With a debugger, you can:
* Explore the state of a running program
* Test implementation code before applying it
* Follow the program's execution logic

Using a debugger, you can set a [breakpoint](https://en.wikipedia.org/wiki/Breakpoint) at any point of
your program to stop it and apply the three points above! Debuggers are very powerful tools and they
can speed up the debugging process a faster than using simple `print()` statements everywhere. 

For those of you who are veteran programmers, you might agree with me that there is a
correlation between the best programmers and the ones that know how to debug effectively. By debugging
effectively, I mean being able to diagnose a problem and then treat the error with minimal difficulty. 
Using a debugger and learning how to use it properly will help you become an effective debugger. It will
take some time before you feel comfortable navigating around in a debugging environment but the purpose
of this tutorial is to get your feet wet before you start using `pdb` in your own code base!


### PDB 101: Intro to `pdb`

So we already talked about the purpose of a debugger, but now it's time to see it in action. First, you
should clone this repo if you haven't already done so. If you don't have `git` installed, I recommend using
it (or some version of source control) and you can find out details on how to install `git` [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
Once you have `git` installed, clone the repo by entering the following in your terminal:

```shell
git clone https://github.com/spiside/pdb-tutorial
```

**NB**: If that didn't work for you, you should follow Github's [cloning tutorial](https://help.github.com/articles/cloning-a-repository/).

Now that you have the repo cloned, let's navigate to the root of the project and take a look at the instructions given:

```shell
cd /path/to/pdb-tutorial
```

`file: instructions.txt`
```shell
Your boss has given you the following project to fix for a client. It's supposed to be a simple dice
game where the object of the game is to correctly add up the values of the dice for 6 consecutive turns.

The issue is that a former programmer worked on it and didn't know how to debug effectively. It's now up to
you to fix the errors and finally make the game playable.

To play the game you must run the main.py file.
```

Seems easy enough! To begin, let's try playing the game to see what's wrong. To run the program, type the following in your
terminal:

```shell
python main.py
``` 
