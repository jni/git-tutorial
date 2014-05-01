Title:  Git and Github: a tutorial
Author: Juan Nunez-Iglesias
Email:  jni.soma@gmail.com

# <center> Git and Github: a tutorial </center>

# Git theory

How does a repository work?

 - hashes
 - diffs
 - branches
 - tags
 - merges
 - .git directory
 - .gitignore file

# Working with Git

 - checkouts, commits, sprouting.
 - working copy, staging area, history
 - *trust* git. Your data is safe. It’s very hard to actually lose data/code.

A bugbear of mine and others': messages should be in the present imperative. “Fix bug X”, “Add feature Y”, “Document function Z”. This will mesh with git’s automatic commit messages, e.g. "Merge branch A from repository B". Find more information about conventions to follow in git log messages [here](http://365git.tumblr.com/post/3308646748/writing-git-commit-messages).

## Exercise 1: git basics

Exercise: initialise a repo, make some changes, git add, git commit (x2). git checkout master -b mult; add multiplication in two commits. Then check out master, add usage string. Then git merge.

First, we start a project, initialising the git repository in an empty directory.

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

Now we commit those changes before changing to a different _branch_. We do this when we want to introduce new functionality that might break our existing code. (In practice, this is whenever we want to change anything!)

```bash
$ git commit -a # add all changes to tracked files
$ git checkout master -b mult # make branch to add multiplication
$ vim calc.py # add command structure
```

The first set of changes lays the foundation for the new structure of the program, which will allow arbitrary operations to the input.

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

Create a branch, make some changes, then make overlapping changes in the master branch. When you try to merge, you will have a conflict that you'll have to fix.

# Working with Github

Github is useful as a semi-centralised model.

 - Creating a new repository: GitHub will offer nice conveniences, such as
   adding a README file, a .gitignore file, and a license.
 - Choosing a license: No license implies closed source. The most popular licenses are BSD and GPL.
 - forks and pull requests

Exercise. Pair up. Alice creates calc. repository. Bob *forks* that repository, creates a branch, makes changes, pushes branch to their fork, and then initiates a *pull request*. Original repo makes comments. Bob updates their branch, pushes to their own repo… PR is magically updated!

Principle: *you* should be the one doing the work. Maintaining a big open source project is work enough.

Exercise. Work in threes. Alice creates calc. repository. Bob and Charlie each fork. Bob makes one set of changes, Charlie makes slightly incompatible set of changes. Bob’s changes get merged by Alice. Charlie needs to do a rebase.

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

