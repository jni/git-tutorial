---
layout: page
permalink: working-with-git/
title: Working with Git
---

In this section, we will get some hands-on experience with the above concepts:

 - getting started with git
 - checkouts, commits, sprouting
 - working copy, staging area, history
 - pushing and pulling

Throughout, an important message is to *trust* git. Your data is safe. It’s
very hard to actually lose data/code.

## Configuring git

Your git settings live in a file called `.gitconfig` in your home directory:
`/Users/username/` in Mac OS X, `$HOME`, usually `/home/username/` in Linux,
and `C:\Users\username\` in Windows (7 and above).

The first thing you should do after installing git is setting your name and
email, so that your changes are properly attributed when you commit them to a
repo:

{% highlight console %}
$ git config --global user.name "Juan Nunez-Iglesias"
$ git config --global user.email "juan.n@unimelb.edu.au"
{% endhighlight %}

(The `--global` tag means that this configuration should be used for all your
git projects. You can change the setting for specific project, e.g. if you want
to use a different institute's email address, by using the command without the
tag inside a specific project.)

## Exercise 1: tracking your change history with git

Let's jump right in!

Git is a tool to control revisions. GitHub is a web service centered on
git. It provides repository hosting as well as a suite of workflows to
aid collaboration.

You will learn a bit more about GitHub in later sections, but for now,
you should create your new project on GitHub, as it offers some nice
conveniences: addition of a README, a gitignore, and a license.

> A quick note on licenses:
> 
> The two most popular free and open source (FOSS) licenses are the
> GNU Public License (GPL) and the BSD 3-clause license. The main
> difference between them is that GPL forces users of your code to
> publish their own code under the GPL itself, while the BSD makes
> no such prescriptions.

Follow [GitHub's
instructions](https://help.github.com/articles/creating-a-new-repository)
to create a new repository called "calc", with the description,
"A simple Python calculator." Choose "initialise with a README", add a
"Python" .gitignore, and the BSD 3-clause license.

Once created, copy the URL from the box in the bottom right, and open
a terminal window to *clone* the project from GitHub to your local
machine.

{% highlight console %}
$ git clone git@github.com:jni/calc.git
$ cd calc
{% endhighlight %}

(Note: "jni" should be replaced by your own username, above.)

Write a simple Python script, `calc.py` to sum all command line
arguments together:

{% highlight python %}
import sys
print(sum(map(int, sys.argv[1:])))
{% endhighlight %}

Make sure it works:

{% highlight console %}
$ python calc.py 7 9
16
{% endhighlight %}

Tell git to add the file to its tracking system, and _commit_ your changes to
the repository:

{% highlight console %}
$ git add calc.py
$ git commit
{% endhighlight %}

You'll be asked to enter a *commit message*. This should summarise the changes
you made and *why* you made them. The message should be readable without (much)
context.

### An aside on style

A bugbear of mine and others': messages should be in the present imperative.
“Fix bug X”, “Add feature Y”, “Document function Z”. This will mesh with git’s
automatic commit messages, e.g. "Merge branch A from repository B". Find more
information about conventions to follow in git log messages
[here](http://365git.tumblr.com/post/3308646748/writing-git-commit-messages).
If you don't, Linus Torvalds will come after you and your family.

In addition to the above English style guidelines, two other conventions are
followed in the community:

> Ensure first line is at most 50 chars long
> 
> It should also not have a period at the end. The subsequent lines
> should be separated from the one-line summary by a blank line, and
> should be wrapped at 72 characters. This ensures readability in all
> text terminals.

### ... back to the exercise

Now add the `__main__` guard to the script, as is best practice in
Python:

{% highlight python %}
import sys

if __name__ == '__main__':
    print(sum(map(int, sys.argv[1:])))
{% endhighlight %}

Commit those changes as above.

Finally, add and commit another set of changes, allowing real numbers
rather than just integers.

{% highlight python %}
import sys

if __name__ == '__main__':
    print(sum(map(float, sys.argv[1:])))
{% endhighlight %}

Test your changes again:

{% highlight console %}
$ python calc.py 7.9 2.1
10.0
{% endhighlight %}

You now have a *history* that you can look at and interact with:

{% highlight console %}
$ git log
commit f868a7a83d8344420a89cab37c9d3437a2d6f771
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Thu Aug 21 12:59:02 2014 +1000

    Add float support

commit 2f39ef9e7549ddb80c5e1051507858f29f9c56ec
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Thu Aug 21 12:58:04 2014 +1000

    Add main guard to follow best practice

commit a9a8d720689b42c17f7f519c7559d5e084552a51
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Thu Aug 21 12:56:32 2014 +1000

    Initial commit of main file

commit 0036c68474f1f95d753ded70abdce68d6e9427fb
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Thu Aug 21 12:55:08 2014 +1000

    Initial commit
{% endhighlight %}

You can *check out* earlier versions of your code like so:

{% highlight console %}
$ git checkout a9a8d720689b42c17f7f519c7559d5e084552a51
{% endhighlight %}

Now your code should error if you try using decimal points again:

{% highlight console %}
$ python calc.py 7.9 2.1
Traceback (most recent call last):
  File "calc.py", line 2, in <module>
    print(sum(map(int, sys.argv[1:])))
ValueError: invalid literal for int() with base 10: '7.8'
{% endhighlight %}

> Note: you can use just the first few digits of the hash when checking
> out a specific revision:
> 
> {% highlight console %}
> git checkout a9a8d
> {% endhighlight %}

Thankfully, it's easy to go back to the latest version:

{% highlight console %}
$ git checkout master
{% endhighlight %}

## Exercise 2: branches

Now you will undertake a major change to the structure of the code,
to allow operations other than adding. However, because it's such a big
change, you want to work in a different _branch_ from "master", so that
you can keep using that one while fixing up the new stuff. In practice,
almost _every_ change is significant enough to warrant a new branch,
because "sprouting" one is cheap and easy.

{% highlight console %}
$ git checkout master -b mult # make branch to add multiplication
{% endhighlight %}

The first set of changes lays the foundation for the new structure of the
program, which will allow arbitrary operations to the input.

{% highlight python %}
import sys

def add_all(nums):
    return sum(nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
{% endhighlight %}

Commit those changes:

{% highlight console %}
$ git commit -a
{% endhighlight %}

... and add the multiplication operation:

{% highlight python %}
import sys

def add_all(nums):
    return sum(nums)

def multiply_all(nums):
    return reduce(lambda a, b: a * b, nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
    elif command == 'multiply':
        print(multiply_all(nums))
{% endhighlight %}

Make sure it works:

{% highlight console %}
$ python calc.py multiply 5 6
30.0
{% endhighlight %}

Now commit and return to the master branch for some unrelated changes:

{% highlight console %}
$ git commit -a
$ git checkout master
{% endhighlight %}

Add a docstring to the module:

{% highlight python %}
"""calc.py: A simple Python calculator."""
import sys

if __name__ == '__main__':
    print sum(map(float, sys.argv[1:]))
{% endhighlight %}

Commit those changes, and finally merge in the mult branch. Git does
the heavy lifting for you!

{% highlight console %}
$ git commit -a
$ git merge mult
{% endhighlight %}

Git automatically generates a log message and produces the merged file:

{% highlight python %}
"""calc.py: A simple Python calculator."""
import sys

def add_all(nums):
    return sum(nums)

def multiply_all(nums):
    return reduce(lambda a, b: a * b, nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
    elif command == 'multiply':
        print(multiply_all(nums))
{% endhighlight %}

Easy!

## Exercise 3: merge conflicts

Sometimes, changes in a branch and changes in the master branch are too
difficult to merge automatically, and `git merge` will result in a
*merge conflict*. In this exercise, you will see how to deal with those.

{% highlight console %}
$ git checkout master -b usage
{% endhighlight %}

Add a usage string to your file, which will print when the command is
not recognised:

{% highlight python %}
"""calc.py: A simple Python calculator."""
import sys

def add_all(nums):
    return sum(nums)

def multiply_all(nums):
    return reduce(lambda a, b: a * b, nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
    elif command == 'multiply':
        print(multiply_all(nums))
    else:
        usage = "calc.py [add|multiply] NUM1 [NUM2 [NUM3 [...]]]"
        print(usage)
{% endhighlight %}

Commit your changes:

{% highlight console %}
$ git commit -a
{% endhighlight %}

Now, create a *different* branch from master to add a `min` command,
which will return the smallest of the list of numbers:

{% highlight console %}
$ git checkout master -b minimum
{% endhighlight %}

And edit the file as follows:

{% highlight python %}
"""calc.py: A simple Python calculator."""
import sys

def add_all(nums):
    return sum(nums)

def multiply_all(nums):
    return reduce(lambda a, b: a * b, nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
    elif command == 'multiply':
        print(multiply_all(nums))
    elif command == 'min':
        print(min(nums))
{% endhighlight %}

And commit your changes:

{% highlight console %}
$ git commit -a
{% endhighlight %}

Finally, get back the master branch and try to merge each branch in
sequence:

{% highlight console %}
$ git checkout master
$ git merge minimum
$ git merge usage
{% endhighlight %}

At this point git will, technically speaking, *chuck a hissy fit*, and
refuse to perform the merge:

{% highlight console %}
<git error message>
{% endhighlight %}

You will have to go into the file and manually fix the conflicting
changes. Git places markers on the file where it has found conflicts,
so you can quickly identify those locations and decide on a fix:

{% highlight python %}
blah
{% endhighlight %}

In this case, you need to remove the markers and reorder the clauses so
that `else` comes last. It's also a good opportunity to update the
usage string! Get your file to this state and save it:

{% highlight python %}
"""calc.py: A simple Python calculator."""
import sys

def add_all(nums):
    return sum(nums)

def multiply_all(nums):
    return reduce(lambda a, b: a * b, nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
    elif command == 'multiply':
        print(multiply_all(nums))
    elif command == 'min':
        print(min(nums))
    else:
        usage = "calc.py [add|multiply|min] NUM1 [NUM2 [NUM3 [...]]]"
        print(usage)
{% endhighlight %}

Finally, tell git you've fixed the problem, and the merge will
complete!

{% highlight console %}
$ git add calc.py
$ git commit
{% endhighlight %}
