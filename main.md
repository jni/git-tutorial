Title:  Git and Github: a tutorial
Author: Juan Nunez-Iglesias
Email:  jni.soma@gmail.com

# <center> Git and Github: a tutorial </center>

There are (at least) three stages of git enlightenment:

1. Maintain a linear history for a file or group of files.
2. Use branches effectively to group related changes together, as well as
   maintain parallel versions.
3. Use branches and pull-requests to collaborate effectively.

It took me a long time to pass all three, and I hope this tutorial will help
someone get there faster.

# What is Git and why do you want it?

Git is a _revision control management system_[^git][^gitbook]. It helps you
keep track of changes to your files. You've probably had the following thing
happen to you before:

![PhD Comics:
Final.doc](http://www.phdcomics.com/comics/archive/phd101212s.gif)

I certainly have:

![Revision hell](images/revhell.png)

Notice:

- Different versions of the same document have different filenames.
- The date and author of a change are encoded haphazardly in the filename.
- Serial edits by different authors are concatenated in the filename, with no
  indication as to whose edit came first.
- These are just five files in one directory; other revisions are probably
  scattered around my various hard drives and as email attachments.

This is known as _revision hell_, and it happens when you don't use revision
control, even if you are working alone. Systems such as Git save you from this.

# Git theory

How does a repository work?

(Don't stress this section too much: it will all become second nature after
you've used git for a bit.)

## Hashes and diffs

A *hash* is 160-bit summary of your data. Using an algorithm called SHA-1, Git
mashes up the contents of your files until only 160 bits remain[^hash][^hash2].
SHA1 and related algorithms have the nice property that if your files change
_just a little bit_, their hash changes _dramatically_. For example, here are
the SHA1 hashes of files containing just the string "hello" or "Hello":

```
$ echo "hello" | openssl dgst -sha1
f572d396fae9206628714fb2ce00f72e94f2258f
$ echo "Hello" | openssl dgst -sha1
1d229271928d3f9e2bb0375bd6ce5db6c6d348d9
```

This gives your data security, because if someone messes with it, or if a file
gets subtly corrupted, the hashes will no longer match and git will know about
it.

So, whenever you _commit_ your data to git, it puts it in a special folder
named `.git`, and stores its hash at that time. If you then make changes, git
will only need to store the changes (also called the _diff_), but it will also
store the hash of the data.

With these two things, git knows how to rebuild your data at any given time:
find the last complete copy of the data, and then apply each diff in succession
until the appropriate commit is reached. As additional verification, the hash
of the data is recomputed and compared with the stored hash.

After a certain number of successive diffs, the files may be different enough
that it's worthwhile storing them again from scratch. Git determines all this
automatically. All you need to know is that storing file histories in git is
_cheap_, much, much cheaper than storing multiple slightly different versions
of a file.

## Checkouts

Retrieving an earlier version of your work is then easy:

```
$ git checkout <hash>
```

This finds the version matching the supplied hash in the `.git` directory, and
then copies it out to your working directory.

## Branches and tags

Hashes are unwieldy and difficult to read, so git offers a number of
conveniences to avoid having to deal with them most of the time. The first is
_tags_, which are simply alternate names for specific hashes. Thus, if you are
writing a paper, you can tag the version you submit as follows:

```
$ git tag submitted-version
```

From then on, the name "`submitted-version`" will be associated with that
committed version of your files.

In addition to storing a history of a file, git can store _alternate_
histories! This allows you to easily maintain
different, parallel versions of a file. If you were writing a paper, you might
have two different versions of the introduction, or you might have a version
where you include a table and one where that table is pushed to the
supplementary material. With branches, git helps you keep track of these,
propagate changes from one to the other, and eventually merge them together.

## Merges

In many cases, git can automatically merge branches that hold independent
changes to a file or set of files. To continue with the paper example, if two
different people work on different sections of the paper, Git will be able to
merge the two versions together to update both sections.

## Repositories

The `.git` directory that we have mentioned is known as the _repository_, or
repo for short. It contains all your past history, including the actual data,
the hashes of the data, dates and authors of changes, and tags and branches.

Once upon a time, before git was invented, repositories were always in a
central location such as your department's main computing server. (The most
popular version control system before git, Subversion, followed this model.)
This made sharing of access unnecessarily cumbersome: you had to be very
careful whom you allowed to commit changes to the repository, and you couldn't
access the repository without an internet connection and sometimes a VPN.

In git, repositories can be replicated, either in full or in part, as many
times as necessary. Git hashes enable this: since every version has a commit
hash that is practically guaranteed to be unique, as well as one or more
_parent_ commits that are also hashed, it is trivially easy to reason about
duplicated versions of the history.

In addition to enabling backups, this also helps with collaboration, but adds
an extra layer that you need to consider when using git: in addition to your
repository in `.git` and your working copy, there are also "clone"
repositories, also called "forks". These are useful for backups and to
collaborate with others.

While we *commit to* a repo from a working copy, and we *check out* to a
working copy from a repo, we *push to* a fork from our repo, or *fetch from* a
fork to our repo.

# Working with Git

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

```
$ git config --global user.name "Juan Nunez-Iglesias"
$ git config --global user.email "juan.n@unimelb.edu.au"
```

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

```bash
$ git clone git@github.com:jni/calc.git
$ cd calc
```

(Note: "jni" should be replaced by your own username, above.)

Write a simple Python script, `calc.py` to sum all command line
arguments together:

```python
import sys
print(sum(map(int, sys.argv[1:])))
```

Make sure it works:

```
$ python calc.py 7 9
16
```

Tell git to add the file to its tracking system, and _commit_ your changes to
the repository:

```bash
$ git add calc.py
$ git commit
```

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

```
Ensure first line is at most 50 chars long

It should also not have a period at the end. The subsequent lines
should be separated from the one-line summary by a blank line, and
should be wrapped at 72 characters. This ensures readability in all
text terminals.
```

### ... back to the exercise

Now add the `__main__` guard to the script, as is best practice in
Python:

```python
import sys

if __name__ == '__main__':
    print(sum(map(int, sys.argv[1:])))
```

Commit those changes as above.

Finally, add and commit another set of changes, allowing real numbers
rather than just integers.

```python
import sys

if __name__ == '__main__':
    print(sum(map(float, sys.argv[1:])))
```

Test your changes again:

```
$ python calc.py 7.9 2.1
10.0
```

You now have a *history* that you can look at and interact with:

```
$ git log
```

You can *check out* earlier versions of your code like so:

```
$ git checkout <hash>
```

Now your code should error if you try using decimal points again:

```
$ python calc.py 7.9 2.1
Traceback (most recent call last):
  File "calc.py", line 2, in <module>
    print(sum(map(int, sys.argv[1:])))
ValueError: invalid literal for int() with base 10: '7.8'
```

> Note: you can use just the first few digits of the hash when checking
> out a specific revision:

> ```
> git checkout <truncated-hash>
> ```

Thankfully, it's easy to go back to the latest version:

```
$ git checkout master
```

## Exercise 2: branches

Now you will undertake a major change to the structure of the code,
to allow operations other than adding. However, because it's such a big
change, you want to work in a different _branch_ from "master", so that
you can keep using that one while fixing up the new stuff. In practice,
almost _every_ change is significant enough to warrant a new branch,
because "sprouting" one is cheap and easy.

```bash
$ git checkout master -b mult # make branch to add multiplication
```

The first set of changes lays the foundation for the new structure of the
program, which will allow arbitrary operations to the input.

```python
import sys

def add_all(nums):
    return sum(nums)

if __name__ == '__main__':
    command = sys.argv[1]
    nums = map(float, sys.argv[2:])
    if command == 'add':
        print(add_all(nums))
```

Commit those changes:

```bash
$ git commit -a
```

... and add the multiplication operation:

```python
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
```

Now commit and return to the master branch for some unrelated changes:

```bash
$ git commit -a
$ git checkout master
```

Add a docstring to the module:

```python
"""calc.py: A simple Python calculator."""
import sys

if __name__ == '__main__':
    print sum(map(float, sys.argv[1:]))
```

Commit those changes, and finally merge in the mult branch. Git does
the heavy lifting for you!

```bash
$ git commit -a
$ git merge mult
```

Git automatically generates a log message and produces the merged file:

```python
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
```

Easy!

## Exercise 3: merge conflicts

Sometimes, changes in a branch and changes in the master branch are too
difficult to merge automatically, and `git merge` will result in a
*merge conflict*. In this exercise, you will see how to deal with those.

```
$ git checkout master -b usage
```

Add a usage string to your file, which will print when the command is
not recognised:

```python
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
```

Commit your changes:

```
$ git commit -a
```

Now, create a *different* branch from master to add a `min` command,
which will return the smallest of the list of numbers:

```
$ git checkout master -b minimum
```

And edit the file as follows:

```python
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
```

And commit your changes:

```
$ git commit -a
```

Finally, get back the master branch and try to merge each branch in
sequence:

```
$ git checkout master
$ git merge minimum
$ git merge usage
```

At this point git will, technically speaking, *chuck a hissy fit*, and
refuse to perform the merge:

```
<git error message>
```

You will have to go into the file and manually fix the conflicting
changes. Git places markers on the file where it has found conflicts,
so you can quickly identify those locations and decide on a fix:

```python
blah
```

In this case, you need to remove the markers and reorder the clauses so
that `else` comes last. It's also a good opportunity to update the
usage string! Get your file to this state and save it:

```python
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
```

Finally, tell git you've fixed the problem, and the merge will
complete!

```
$ git add calc.py
$ git commit
```

# Working with Github

From earlier:

> Git is a tool to control revisions. GitHub is a web service centered
> on git. It provides repository hosting as well as a suite of
> workflows to aid collaboration.

In this section you will learn about pushing, pulling, forks, and
pull requests.

## Mini-exercise: pushing your project back to GitHub

You started out creating a project on GitHub, which you cloned to your
own computer. All the changes you recorded since then are stored
*locally*, on your computer. You need to explicitly tell git that you
want to *push* the changes back to GitHub.

To understand how to do this, you need to keep in mind that git is
distributed: every copy of your data contains the entire history. There
is nothing special about your local copy vs GitHub's copy vs the copy
on your department's computing servers. Therefore, you have to deal
with "remotes", which are your local git's address book of other copies
of the history. Check it out:

```bash
$ git remote -v
```

That tells you the *name* of a remote, and its location. "origin" is
the default name for the remote from which you cloned the current
repository.

Now, push your local changes back to the origin:

```bash
$ git push origin master:master
```

Read the above as "push to origin my branch "master" onto its branch
"master".

After this, you'll be able to refresh your repo page on GitHub and
browse your code's history.

## Exercise 4: GitHub pull requests

Exercise. Pair up. Alice creates calc. repository. Bob *forks* that repository,
creates a branch, makes changes, pushes branch to their fork, and then
initiates a *pull request*. Original repo makes comments. Bob updates their
branch, pushes to their own repo... PR is magically updated!

## Exercise 4: Rebasing a GitHub PR

Principle: _you_ should be the one doing the work. Maintaining a big open
source project is work enough. Therefore, if your merge does not apply cleanly
(because the master has changed since you started your work), you need to
_rebase_ your changes on top of the current master.

Exercise. Work in threes. Alice creates calc. repository. Bob and Charlie each
fork. Bob makes one set of changes, Charlie makes slightly incompatible set of
changes. Bob’s changes get merged by Alice. Charlie needs to do a rebase.

```
git checkout master
git remote add upstream <url>
git pull upstream master
git rebase master my-branch
```

<fix conflicted files>

```
git add <conflicted-file>
git rebase --continue
git push -f origin my-branch:my-branch
```

# Advanced topics

 - Releases
 - cherry-picking
 - squashing commits
 - travis-CI
 - PRs against PRs

# References

[^git]: http://git-scm.com/ The Git homepage. A plethora of resources.

[^gitbook]: http://git-scm.com/book The Git book. Your starting point for all
    git knowledge.

[^hash]: http//alblue.bandlem.com/2011/08/git-tip-of-week-objects.html
    A technical description of git hashing.

[^hash2]: http://www.ericsink.com/vcbe/html/cryptographic_hashes.html An
    introduction to cryptographic hashing
