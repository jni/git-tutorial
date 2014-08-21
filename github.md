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

For this exercise you will have to pair up with your neighbour, which
we will name Alice. (And your name is Bob, in keeping with the computer
science literature.)

Delete your "calc" repository on GitHub (this is done under "Settings"
in the right-hand menu). You've realised that Alice has her own and
that you can both save effort by collaborating.

Navigate to Alice's repository on GitHub, and click the "Fork" button.
This will create a copy of Alice's repo on your GitHub account, which
you can then clone on your machine as before.

You want to add the "max" function to the list of available commands.
Remember to *create a new branch!*

```
$ git checkout master -b max
```

Edit the `calc.py` file to look like so (notice the new "max" command):

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
    elif command == 'max':
        print(max(nums))
    else:
        usage = "calc.py [add|multiply|min] NUM1 [NUM2 [NUM3 [...]]]"
        print(usage)
```

Now commit those changes and push them to a new branch on GitHub:

```bash
$ git commit -a
$ git push origin max:max
```

Go to the GitHub page for the project. You should see a new button
showing that you've recently updated a branch and prompting you to
*initiate a pull request*. This will tell Alice that you've made some
changes to the code and you would like her to incorporate them into
her project. Notice that you did this *without needing any special
access from Alice!*. This is the magic of GitHub.

(Ed Schofield's slide about commits to NumPy et al.)

Click on the PR button and fill in the form. Filling in a useful
title and message here is very important!

Alice should get an email notification that there is a pull request to
her project. Clicking on it, she will be taken to the web form for the
PR, where she can examine the changes that Bob has made.

Alice will notice that the "usage" string is now out of date. She can
comment directly on the relevant lines of code in the PR, letting Bob
know that this needs fixing before she will accept his changes.

You can now go back to your computer, and make the requested change:
update this line:

```python
        usage = "calc.py [add|multiply|min] NUM1 [NUM2 [NUM3 [...]]]"
```

to look like this:

```python
        usage = "calc.py [add|multiply|min|max] NUM1 [NUM2 [NUM3 [...]]]"
```

Again, commit and push your changes:

```bash
$ git commit -a
$ git push origin max:max
```

If both you and Alice go back to the PR page, you will see that it has
been automagically updated with your new changes! (You may need to
refresh the page.)

Alice, satisfied with the update, can now click on the "Merge pull
request" button and incorporate Bob's changes to her code!

## Notes on contributing to open source projects

- Always look at their README file, and their CONTRIBUTING file if they
  have one. Read them carefully so that you follow their conventions.
- You can expect to do most of the work. Maintaining a large open
  source project is a lot of work, and authors rarely have the time to
  clean up after you.
- When in Rome, do as the Romans do. Look at their existing codebase
  and try to follow their example. (This is not to say that you can't
  improve on it; but make sure your documentation and testing *at
  least* meets their standards.)

## Notes on why you should make your own code open source

- The idea that you will be scooped by someone looking at your code is
  ludicrous. Reading code is *hard work* and no one will do it on the
  off chance that there is a Nature paper buried in there.
- Your own coding habits and practice will improve just by knowing that
  it's out there. (Shame is a powerful motivator! =P)
- If someone *does* look at your code and find it useful, chances are
  that you will have gained a new collaborator, not a competitor.
- It is just good scientific practice!

## Exercise 5: Rebasing a GitHub PR

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
