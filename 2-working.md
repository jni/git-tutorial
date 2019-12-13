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

```console
$ git config --global user.name "Your Name"
$ git config --global user.email "your.name@your.institute.edu"
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
> The three most popular free and open source (FOSS) licenses are the
> GNU Public License (GPL), the MIT license, and the BSD 3-clause license.
> The main difference between them is that the GPL requires released
> modifications, and even linking software, to themselves be licensed under
> GPL-compatible licenses, while MIT and BSD make no such prescriptions.
> 
> I'm a big proponent of BSD. Jake Vanderplas, Director of Research at
> the University of Washington's eScience Institute, has a fantastic
> [blog post](http://www.astrobetter.com/blog/2014/03/10/the-whys-and-hows-of-licensing-scientific-code/)
> explaining why.
>
> Having said that, as Jake says, what's important is that you license
> your software and use one of the above licenses. Which license you use is
> a personal choice, not a technical one. A good rule of thumb is, when in
> Rome, do as the Romans do: if writing scientific Python, use BSD. If
> writing R packages, use GPL.

Follow [GitHub's
instructions](https://help.github.com/articles/creating-a-new-repository)
to create a new repository called "pycalc", with the description,
"A function to do simple arithmetic from strings."

Once created, copy the URL from the box in the bottom right, and open
a terminal window to *clone* the project from GitHub to your local
machine.

```console
$ git clone git@github.com:<username>/pycalc.git
$ cd pycalc
```

You can type `git status` to see that there is a git-tracked project there,
but nothing to add to the history:

```console
$ git status
```

Now you can start working on the project. We are going to create a function
that takes in strings like '2 + 34' and produce the result, 36.

In your editor, type:

~~~python
def compute(expression):
    values = expression.split(' ')
    num0 = int(values[0])
    operator = values[1]
    num1 = int(values[2])
    if operator == '+':
        return num0 + num1
    else:
        print('unknown operator!')
        return 0
~~~

And save it to a file called `calc.py` in the `pycalc` directory.

Now we can see that git knows about the file, but it doesn't know whether it
needs to worry about it. That's why it's listed under "Untracked files".
This is handy for playing around with external files (e.g. dummy data files)
that you don't necessarily want to keep for posterity.

```console
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    calc.py

    nothing added to commit but untracked files present (use "git add" to
    track)
```

Tell git to add the file to its tracking system:

```console
$ git add calc.py
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   calc.py
```

Then, *commit* the changes to the history:

```console
$ git commit --message "Initial work on a Python string calculator"
[master c85a965] Initial work on a Python string calculator
 1 file changed, 12 insertions(+)
 create mode 100644 calc.py
```

The quoted string is called a *commit message*. This should summarise the
changes you made and *why* you made them. The message should be readable
without (much) context.

You may want to enter a much longer message (one or two paragraphs). For that,
you will need to *set up a git editor* (instructions
[here](https://help.github.com/articles/associating-text-editors-with-git/))
and type just `git commit`.

### An aside on style

A bugbear of mine and others': messages should be in the present imperative.
“Fix bug X”, “Add feature Y”, “Document function Z”, rather than past (Fixed,
Added, Documented) or present (Fixes, Adds, Documents). This meshes with git’s
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

We shouldn't return 0 on error, because it can be confused with a perfectly
valid result. Instead, Python provides a handy value for missing or invalid
data:
`None`.

Edit your file so that, if the operator is not recognised, it returns `None`
instead of 0.

~~~python
def compute(expression):
    values = expression.split(' ')
    num0 = int(values[0])
    operator = values[1]
    num1 = int(values[2])
    if operator == '+':
        return num0 + num1
    else:
        print('unknown operator!')
        return None
~~~

As before, we can check the status:

```console
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   calc.py

no changes added to commit (use "git add" and/or "git commit -a")
```

This time, the output is a bit different: rather than showing you an
"Untracked file", your changes to `calc.py` are now listed as "not staged
for commit". What does this mean?

If you think of git as taking snapshots of changes over the life of a
project, `git add` specifies *what* will go in a snapshot by putting them
into a *staging area*. Then, `git commit` *actually takes* the snapshot,
making a permanent record of it. This two-step process allows you to group
related changes together. For example, when writing a paper, you might
want to commit your new Discussion section, but wait to add your as-yet
unfinished changes to the Introduction.

If you don't have anything staged when you type `git commit`, git will
prompt you to use `git commit -a` or `git commit --all`, which is kind
of like saying, "ok, *everyone in this one!*". However, it's almost
always better to explicitly add things to the staging area, because
you might otherwise commit changes you forgot you made. Going back to
the snapshots analogy, you might get the extra with the incomplete
makeup walking in and ruining the picture because you used `-a`! Try
to stage things manually, or you might find yourself searching for
"git undo commit" more than you would like!

Ok let's just commit everything now. =P

BUT FIRST! Let's actually check that "everything" is what we want. In
addition to the one-line, "files changed" summary that we've seen, we can
ask git for a line-by-line summary of changes:

```console
 $ git diff
diff --git a/calc.py b/calc.py
index ffadff3..012d92a 100644
--- a/calc.py
+++ b/calc.py
@@ -7,4 +7,4 @@ def compute(expression):
         return num0 + num1
     else:
         print('unknown operator!')
-        return 0
+        return None
```

You can see that changes to a line are encoded in git as deletion of
that line and addition of the modified line. Git works on a line by line
basis, which is why it's perfect for code and plain text files, but not
so great for big binary files such as images.

At any rate, the list of all the changes is indeed what we want to commit,
so we can use `--all`:

```console
$ git commit --all --message "Return None, not 0, on invalid input"
```

Finally, let's add support for subtraction:

~~~python
def compute(expression):
    values = expression.split(' ')
    num0 = int(values[0])
    operator = values[1]
    num1 = int(values[2])
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    else:
        print('unknown operator!')
        return None
~~~

Let's commit the changes:

```console
$ git add calc.py
$ git commit -m "Add support for subtraction"
```

(`-m` is a shortcut for `--message`.)

You now have a *history* that you can look at and interact with:

```console
$ git log
commit a4e5f6d6c9bd5dbcad86b4c5269b9c1995a1f321 (HEAD -> master)
Author: Juan Nunez-Iglesias <juan.nunez-iglesias@monash.edu>
Date:   Tue Dec 10 16:21:35 2019 +1100

    Add support for subtraction

commit 18b68bcab248b571e4c4264c43b013c1cedbd7d5
Author: Juan Nunez-Iglesias <juan.nunez-iglesias@monash.edu>
Date:   Tue Dec 10 16:16:33 2019 +1100

    Return None, not 0, on invalid input

commit f817c9aeb2ff15e3b3a91a8a9124c87f41a6cbb8
Author: Juan Nunez-Iglesias <juan.nunez-iglesias@monash.edu>
Date:   Tue Dec 10 16:14:24 2019 +1100

    Initial work on a Python string calculator
```

You can *check out* earlier versions of your code using the hash of
a particular snapshot, like so:

```console
$ git checkout 18b68bcab248b571e4c4264c43b013c1cedbd7d5
Note: switching to '18b68bcab248b571e4c4264c43b013c1cedbd7d5'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 18b68bc Return None, not 0, on invalid input
```

You can verify that the file in your directory is now the older
version:

```console
$ cat calc.py
def compute(expression):
    values = expression.split(' ')
    num0 = int(values[0])
    operator = values[1]
    num1 = int(values[2])
    if operator == '+':
        return num0 + num1
    else:
        print('unknown operator!')
        return None
```

> Note: you can use just the first few digits of the hash when checking
> out a specific revision:
> 
> ```console
> $ git checkout f817c
> Previous HEAD position was 18b68bc Return None, not 0, on invalid input
> HEAD is now at f817c9a Initial work on a Python string calculator
> ```

Thankfully, it's easy to go back to the latest version:

```console
$ git switch master
Previous HEAD position was c0a11b0... Initial work on a Python string calculator
Switched to branch 'master'
```

## Exercise 2: branches

Now we will undertake a major change to the structure of the function.
Because it's such a big
change, you want to work in a different _branch_ from "master", so that
you can keep using that one while fixing up the new stuff. In practice,
almost _every_ change is significant enough to warrant a new branch,
because "sprouting" one is cheap and easy.

```console
$ git switch --create use-unpacking
```

You should read that as: switch into a *new* branch
called `use-unpacking`.
(Note: if using a git version older than 2.23.0, you should use `git checkout
--branch use-unpacking`.)

Now let's use *iterable unpacking* to get the values from our string:

~~~python
def compute(expression):
    num0, operator, num1 = expression.split(' ')
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    else:
        print('unknown operator!')
        return None
~~~

Commit that change:

```console
$ git add calc.py
$ git commit -m "Use fancy-schmancy iterable unpacking"
```

... Oops! It looks like we've broken our function!

```python
>>> import calc
>>> calc.compute('5 + 8')
'58'
```

Meanwhile, our supervisor wants a working version of the program *now*! No time
to debug. Plus, she has no patience for broken software! Or
functions without documentation! Let's go back to our working version:

```console
$ git switch master
```

And edit the file to add an informative comment above the function
definition:

```python
# Perform simple arithmetic encoded in an input string:
# '1 + 2' -> 3, or '1 - 2' -> -1.
def compute(expression):
    values = expression.split(' ')
    num0 = int(values[0])
    operator = values[1]
    num1 = int(values[2])
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    else:
        print('unknown operator!')
        return None
```

We can commit those changes:

```console
$ git commit -a -m "Add function documentation"
[master ef26741] Add function documentation
 1 file changed, 2 insertions(+)
```

Whew! Now we have that working version (the master branch should always
work!), with documentation, we can send it to our supervisor and go back to
fixing our fancy iterable-unpacking version.

```console
$ git switch use-unpacking
```

Now, more calmly, we see than in our rush to implement unpacking, we've
forgotten to convert our strings to numbers! A simple fix:

```python
def compute(expression):
    num0, operator, num1 = expression.split(' ')
    num0, num1 = int(num0), int(num1)
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    else:
        print('unknown operator!')
        return None
```

And we commit it:

```console
 $ git commit -a -m "Convert num strings to int"
[use-unpacking 81794b8] Convert num strings to int
 1 file changed, 1 insertion(+)
```

Now your `use-unpacking` branch is ready to become the main
branch of your program. But you also don't want to throw out the documentation
changes you made on the master branch!

`git merge` can often automatically reconcile changes in two branched
histories:

```console
 $ git switch master
Switched to branch 'master'

 $ git merge use-unpacking
Auto-merging calc.py
Merge made by the 'recursive' strategy.
 calc.py | 6 ++----
 1 file changed, 2 insert:w
 ions(+), 4 deletions(-)
```

Git has automatically done the work of resolving the different changes!

```python
# Perform simple arithmetic encoded in an input string:
# '1 + 2' -> 3, or '1 - 2' -> -1.
def compute(expression):
    num0, operator, num1 = expression.split(' ')
    num0, num1 = int(num0), int(num1)
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    else:
        print('unknown operator!')
        return None
```


## Exercise 3: merge conflicts

Sometimes, it's not clear *how* two branching changes should be merged, for
example if the same line is changed in both histories. Which change should
take precedence? Git does the right thing and defers the decision to you.
Let's see how it does this.

Create a branch to add the multiplication operator:

```console
$ git switch master --create add-multiplication
```

It's just a matter of adding the following lines:

```python
    elif operator == '*':
        return num0 * num1
```

Commit that change:

```console
$ git commit -a -m "Add support for multiplication operator"
```

Now, repeat the same procedure, including the new branch, for division:

```console
$ git switch master --create add-division
# ... make changes to python file...
$ git commit -a -m "Add support for division operator"
```

Finally, bring those changes into the master branch:

```console
$ git switch master
$ git merge add-multiplication
$ git merge add-division
```

At this point git will, to use a technical term, *chuck a hissy fit*, and
refuse to perform the merge:

```console
Auto-merging calc.py
CONFLICT (content): Merge conflict in calc.py
Automatic merge failed; fix conflicts and then commit the result.
```

Note: git error messages can be scary, but it's worth taking the time to read
them carefully. They often provide the solution to the current problem.

In this case, we must go into the file and manually fix the conflicting
changes. Git places markers on the file where it has found conflicts,
so you can quickly identify those locations and decide on a fix:

~~~
# Perform simple arithmetic encoded in an input string:
# '1 + 2' -> 3, or '1 - 2' -> -1.
def compute(expression):
    num0, operator, num1 = expression.split(' ')
    num0, num1 = int(num0), int(num1)
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
<<<<<<< HEAD
    elif operator == '*':
        return num0 * num1
=======
    elif operator == '/':
        return num0 / num1
>>>>>>> add-division
    else:
        print('unknown operator!')
        return None
~~~

Git is saying that in the current branch ("HEAD"), the file at that
position contains the multiplication lines, while the
`add-division` branch, which we are trying to merge, contains the division
lines *at the very same spot*.

You fix merge conflicts by navigating to the `<<<<<<<` markers, comparing
the two conflicting versions, and leaving the file as you want it to be
committed: removing the markers, and combining the changes in a way
that makes sense, and that a system like git can't figure out by itself.

In this case, we simply remove all the markers, and leave the two line pairs
one after the other inside the file.

~~~python
# Perform simple arithmetic encoded in an input string:
# '1 + 2' -> 3, or '1 - 2' -> -1.
def compute(expression):
    num0, operator, num1 = expression.split(' ')
    num0, num1 = int(num0), int(num1)
    if operator == '+':
        return num0 + num1
    elif operator == '-':
        return num0 - num1
    elif operator == '*':
        return num0 * num1
    elif operator == '/':
        return num0 / num1
    else:
        print('unknown operator!')
        return None
~~~

Finally, tell git you've fixed the problem by `git add`ing the file,
commit, and the merge will complete!

```console
$ git add calc.py
$ git commit
```

You can check that the history of our project using our created alias, `git
lsd`. (See the set up chapter.)

```
 $ git lsd
*   96b6056 (HEAD -> master) Merge branch 'add-division'
|\
| * f07a196 (add-division) Add support for division operator
* | 81d75fc (add-multiplication) Add support for multiplication operator
|/
*   8ab4b31 Merge branch 'use-unpacking'
|\
| * 81794b8 (use-unpacking) Convert num strings to int
| * 215e7b8 Use fancy-schmancy iterable unpacking
* | a0d2ca0 Add function documentation
|/
* a4e5f6d Add support for subtraction
* 18b68bc Return None, not 0, on invalid input
* f817c9a Initial work on a Python string calculator
```

## Challenge: Style guides

When in Rome, do as the Romans do. In the case of Python, the community has
converged on some prescriptive coding styles. The two main authoritative style
guides are [PEP8](https://www.python.org/dev/peps/pep-0008/) (for syntax) and
[PEP257](https://www.python.org/dev/peps/pep-0257/) (for documentation). Can
you change your function's documentation to match the recommendations of
PEP257?
