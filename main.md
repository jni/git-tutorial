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

# Working with Git

 - checkouts, commits, sprouting.
 - working copy, staging area, history
 - *trust* git. Your data is safe. It’s very hard to actually lose data/code.

A bugbear of mine and others': messages should be in the present imperative.
“Fix bug X”, “Add feature Y”, “Document function Z”. This will mesh with git’s
automatic commit messages, e.g. "Merge branch A from repository B". Find more
information about conventions to follow in git log messages
[here](http://365git.tumblr.com/post/3308646748/writing-git-commit-messages).

## Exercise 1: git basics

Exercise: initialise a repo, make some changes, git add, git commit (x2). git
checkout master -b mult; add multiplication in two commits. Then check out
master, add usage string. Then git merge.

First, we start a project, initialising the git repository in an empty
directory.

```bash
$ mkdir calculator; cd calculator
$ git init
$ vim calc.py
```

A simple Python script to sum all command line arguments together:

```python
import sys
print sum(map(float, sys.argv[1:]))
```

We then tell git to add the file to its tracking system, and _commit_ our changes to the repository, before making more changes.

```bash
$ git add calc.py
$ git commit
$ vim calc.py
```

Now we add the `__main__` guard to the script, as is best practice:

```python
import sys

if __name__ == '__main__':
    print sum(map(float, sys.argv[1:]))
```

Now we commit those changes before changing to a different _branch_. We do this
when we want to introduce new functionality that might break our existing code.
(In practice, this is whenever we want to change anything!)

```bash
$ git commit -a # add all changes to tracked files
$ git checkout master -b mult # make branch to add multiplication
$ vim calc.py # add command structure
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
        print add_all(nums)
```

We commit those changes before adding a multiplication operation.

```bash
$ git commit -a
$ vim calc.py # add multiplication
```

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
        print add_all(nums)
    elif command == 'multiply':
        print multiply_all(nums)
```

Now we commit and return to the master branch for some unrelated changes:

```bash
$ git commit -a
$ git checkout master
$ vim calc.py # add docstring
```

We add a docstring to the module.

```python
"""calc.py: A simple Python calculator."""
import sys

if __name__ == '__main__':
    print sum(map(float, sys.argv[1:]))
```

We commit those changes, and finally we merge in the mult branch. Git does the heavy lifting for us!

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
        print add_all(nums)
    elif command == 'multiply':
        print multiply_all(nums)
```

## Exercise 2: merge conflicts

Create a branch, make some changes, then make overlapping changes in the master
branch. When you try to merge, you will have a conflict that you'll have to
fix.

# Working with Github

Github is useful as a semi-centralised model.

 - Creating a new repository: GitHub will offer nice conveniences, such as
   adding a README file, a .gitignore file, and a license.
 - Choosing a license: No license implies closed source. The most popular
   licenses are BSD and GPL.
 - forks and pull requests

## Exercise 3: GitHub pull requests

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

git checkout master
git remote add upstream <url>
git pull upstream master
git rebase master my-branch

<fix conflicted files>

git add <conflicted-file>
git rebase --continue
git push -f origin my-branch:my-branch

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
