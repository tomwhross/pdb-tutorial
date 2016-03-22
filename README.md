## `pdb` Tutorial

The purpose of this tutorial is to teach you the basics of `pdb`, the Python DeBugger for [Python2](https://docs.python.org/2/library/pdb.html)
and [Python3](https://docs.python.org/3/library/pdb.html).
It will also include some helpful tricks to make your debugging sessions a lot less stressful. The tutorial works
best if you use Python 2.7 or Python 3.4 and I will highlight the differences between the two versions if a `pdb`
command differs. To check what version of python you're using, type the following in your terminal:

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


### Playing the Game

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

You should see something like this:

```shell
Add the values of the dice
It's really that easy
What are you doing with your life.
Round 1

---------
|*      |
|       |
|      *|
---------
---------
|*      |
|       |
|      *|
---------
---------
|*     *|
|       |
|*     *|
---------
---------
|*     *|
|       |
|*     *|
---------
---------
|*     *|
|   *   |
|*     *|
---------
Sigh. What is your guess?: 
```

Looks like the previous programmer had a sense of...humor? Nonetheless, let's enter 17 (since that is the total value of the dice).

```shell
Sigh. What is your guess?: 17
Sorry that's wrong
The answer is: 5
Like seriously, how could you mess that up
Wins: 0 Loses 1
Would you like to play again?[Y/n]: 
```

Weird. It said the answer is 5 but that's clearly wrong... Alright, maybe the dice addition is wrong but let's try again to figure it out.
Looks like the prompt to play again is `'Y'` so let's enter that now.

```shell
Would you like to play again?[Y/n]: Y
Traceback (most recent call last):
  File "main.py", line 12, in <module>
    main()
  File "main.py", line 8, in main
    GameRunner.run()
  File "/Users/Development/pdb-tutorial/dicegame/runner.py", line 62, in run
    i_just_throw_an_exception()
  File "/Users/Development/pdb-tutorial/dicegame/utils.py", line 13, in i_just_throw_an_exception
    raise UnnecessaryError("You actually called this function...")
dicegame.utils.UnnecessaryError: You actually called this function...
```

Ok weird, looks like an exception was thrown even though we used what was supposed to be a valid input. I think it's safe to
say that the program is broken so let's start the debugging process! 


### PDB 101: Intro to `pdb`

It's time to finally work with python's very own debugger `pdb`. The debugger is included in python's standard library and we
use it the same way we would with any python library. First, we have to import the `pdb` module and then call one of its methods
to add a debugging breakpoint in the program. The conventional way to do this is to add the import **and** call at the same line you
would like to stop at. This is the full statement you would want to include:

```python
import pdb; pdb.set_trace()
```

The method [`set_trace()`](https://docs.python.org/3/library/pdb.html#pdb.set_trace) hard codes a breakpoint at the line you place
it. Let's try it now by opening up the `main.py` file and adding the breakpoint on line 8:

`file: main.py` 
```python
from dicegame.runner import GameRunner


def main():
    print("Add the values of the dice")
    print("It's really that easy")
    print("What are you doing with your life.")
    import pdb; pdb.set_trace() # add pdb here
    GameRunner.run()


if __name__ == "__main__":
    main()
```

Cool, now let's try to run `main.py` again and see what happens.

```shell
python main.py
```
```shell
Add the values of the dice
It's really that easy
What are you doing with your life.
> /Users/Development/pdb-tutorial/main.py(9)main()
-> GameRunner.run()
(Pdb) 
```

There we go! We are now in the middle of the running program and we can start poking around. I think the first issue we should
solve is the proper summation of the dice values.

If you are familiar with Python's intepreter, a lot of that knowledge can be transfered to the `pdb` debugger. However, there will be
a couple gotchas that we will get to in the advanced section. Regardless, let's learn a couple commands that will help us solve the
addition issue.


### The 5 `pdb` commands that will leave you speechless

Taken directly from the `pdb` documentation, these are the six commands that, once you learn them, you won't know how you lived
without them.

1. `l(ist)` - Displays 11 lines around the current line or continue the previous listing.
2. `s(tep)` - Execute the current line, stop at the first possible occasion.
3. `n(ext)` - Continue execution until the next line in the current function is reached or it returns.
4. `b(reak)` - Set a breakpoint (depending on the argument provided).
5. `r(eturn)` - Continue execution until the current function returns.

Notice that there are brackets around the last part of every keyword. The brackets indicate that the rest of the word is _optional_ while
when using the command prompt for `pdb`! This saves typing but a major gotcha is if you have a variable name such as `l` or `n`, then the
`pdb` command takes precendence. That is, say you have a variable named `c` in your program and you want to know the value of `c`. Well,
if you type `c` in `pdb`, you will actually be issuing the `c(ontinue)` keyword which executes the program and only stops if it encounters
a break point!

**NB**: I, and many other programmers, discourage the use of short variable names such as `a`, `b`, `gme`, etc. These carry no meaning
and will confuse other people reading your code. I'm only demonstrating the issues you may encounter with `pdb` in the presence of
shortened variable names.

**NNB** Another helpful tool is the following:
`h(elp) - Without argument, print the list of available commands. With a command as argument, print help about that command.`  

For the rest of the tutorial, I will be using the shortned version of the commands and if I use a command that I have not introduced
here, I will explain what it does. So, let's begin with the first one.

#### 1. l(ist) a.k.a. I'm too lazy to open the file containing the source code

```shell
l(ist) [first [,last]]
    List source code for the current file. Without arguments, list 11 lines around the current line
    or continue the previous listing. With one argument, list 11 lines starting at that line.
    With two arguments, list the given range; if the second argument is less than the first, it is a count.
```

**NB** The above description was generated by calling `help()` on `list`. To get the same output, in the `pdb` REPL type `help(l)`.

Using `list`, we can examine the source code of the current file we are in. The arguments for `list` lets you specify a given range
of lines you wish to see which can be helpful if you are in some weird 3rd party package and you are trying to figure out why they
can't get string enconding working _true story_.

**NB** In Python 3.2 and above, you can type `ll` (long list) which shows you source code for the current function or frame. I use
this all the time instead of `l` since it's much better knowing which function you are in than an arbitrary 11 lines around your
current position.

Let's try using `l` now. In your already open `pdb` prompt, type in `l` and look at the output:

```shell
(Pdb) l
  4     def main():
  5         print("Add the values of the dice")
  6         print("It's really that easy")
  7         print("What are you doing with your life.")
  8         import pdb; pdb.set_trace()
  9  ->     GameRunner.run()
 10     
 11     
 12     if __name__ == "__main__":
 13         main()
[EOF]
``` 

If we want to see the whole file, we can call the list function with the range 1 to 13 like so:

```shell
(Pdb) l 1, 13
  1     from dicegame.runner import GameRunner
  2     
  3     
  4     def main():
  5         print("Add the values of the dice")
  6         print("It's really that easy")
  7         print("What are you doing with your life.")
  8         import pdb; pdb.set_trace()
  9  ->     GameRunner.run()
 10     
 11     
 12     if __name__ == "__main__":
 13         main()
```

Unfortunately, we don't get that much information from this file alone but we do see that it is calling the `run()` method of the `GameRunner`
class. At this point, you might be thinking, "Awesome, I'll just set a `pdb` in the run method from this file `dicegame/runner.py`!" That will
work, but there's an even easier way using the `step()` function we will discuss next.

#### 2. `s(tep)` a.k.a let's see what this method does...

```shell
s(tep)
    Execute the current line, stop at the first possible occasion
    (either in a function that is called or in the current
    function).
```

Your current line of execution should still be on `:9` and you can tell the current line by looking at the `->` outputted by the `list` command. 
Let's call the `step` command and see what happens.

```shell
(Pdb) s
--Call--
> /Users/Development/pdb-tutorial/dicegame/runner.py(22)run()
-> @classmethod
```

Nice! We're currently in the `runner` module on line 22 which we can tell from this line: `> /Users/Development/pdb-tutorial/dicegame/runner.py(22)run()`.
The problem is, we don't have much context so run the `list` command to checkout the method.

```shell
(Pdb) l
 17             total = 0
 18             for die in self.dice:
 19                 total += 1
 20             return total
 21     
 22  ->     @classmethod
 23         def run(cls):
 24             # Probably counts wins or something.
 25             # Great variable name, 10/10.
 26             c = 0
 27             while True:
```

Sweet, now we have some more context on the `run()` method but we are currently on `:22`. Let's step in one more time so that we enter the method itself and
then run the list command to see our current position.

```shell
(Pdb) s
> /Users/Development/pdb-tutorial/dicegame/runner.py(26)run()
-> c = 0
(Pdb) l
 21     
 22         @classmethod
 23         def run(cls):
 24             # Probably counts wins or something.
 25             # Great variable name, 10/10.
 26  ->         c = 0
 27             while True:
 28                 runner = cls()
 29     
 30                 print("Round {}\n".format(runner.round))
 31  
```

As we can see, we are on a terribly named `c` variable that will cause us a major issue if we try to type it out (remember the comment from earlier regarding the
`continue` command). At this point, we are just before the `while` loop so let's enter the loop and see what else we can uncover.

#### `n(ext)` a.k.a I hope this current line doesn't throw an exception

```shell
n(ext)
    Continue execution until the next line in the current function
    is reached or it returns.
```

From the current line, type the `next` command followed by `list` (notice a pattern) and let's observe what happens.

```shell
(Pdb) n
> /Users/Development/pdb-tutorial/dicegame/runner.py(27)run()
-> while True:
(Pdb) l
 22         @classmethod
 23         def run(cls):
 24             # Probably counts wins or something.
 25             # Great variable name, 10/10.
 26             c = 0
 27  ->         while True:
 28                 runner = cls()
 29     
 30                 print("Round {}\n".format(runner.round))
 31     
 32                 for die in runner.dice:
```

Now our current line is on the `while True` statement! We can keep calling `next` indefinitely until the program throws an exception or terminates. Call `next` 3 more
times to get to the `for` loop and then follow it again by `list`.

```shell
(Pdb) n
> /Users/spiro/Development/mobify/engineering-meeting/pdb-tutorial/dicegame/runner.py(28)run()
-> runner = cls()
(Pdb) n
> /Users/spiro/Development/mobify/engineering-meeting/pdb-tutorial/dicegame/runner.py(30)run()
-> print("Round {}\n".format(runner.round))
(Pdb) n
Round 1

> /Users/spiro/Development/mobify/engineering-meeting/pdb-tutorial/dicegame/runner.py(32)run()
-> for die in runner.dice:
(Pdb) l
 27             while True:
 28                 runner = cls()
 29     
 30                 print("Round {}\n".format(runner.round))
 31     
 32  ->             for die in runner.dice:
 33                     print(die.show())
 34     
 35                 guess = input("Sigh. What is your guess?: ")
 36                 guess = int(guess)
```

