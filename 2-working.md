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
$ git config --global user.name "Your Name"
$ git config --global user.email "your.name@your.institute.edu"
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
to create a new repository called "r-string-calc", with the description,
"A function to do simple arithmetic from strings."

Once created, copy the URL from the box in the bottom right, and open
a terminal window to *clone* the project from GitHub to your local
machine.

{% highlight console %}
$ git clone git@github.com:<username>/r-string-calc.git
$ cd r-string-calc
{% endhighlight %}

You can type `git status` to see that there is a git-tracked project there,
but nothing to add to the history:

{% highlight console %}
$ git status
{% endhighlight %}

Now you can start working on the project. We are going to create a function
that takes in strings like '2 + 34' and produce the result, 36.

In your editor, type:

~~~R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  if (operator == '+') {
    return(num0 + num1)
  } else {
    print('unknown operator!')
    return(0)
  }
}
~~~

And save it to a file called `strcalc.R` in the `r-string-calc` directory.

Now we can see that git knows about the file, but it doesn't know whether it
needs to worry about it. That's why it's listed under "Untracked files".
This is handy for playing around with external files (e.g. dummy data files)
that you don't necessarily want to keep for posterity.

{% highlight console %}
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    strcalc.R

    nothing added to commit but untracked files present (use "git add" to
    track)
{% endhighlight %}

Tell git to add the file to its tracking system:

{% highlight console %}
$ git add strcalc.R
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   strcalc.R
{% endhighlight %}

Then, *commit* the changes to the history:

{% highlight console %}
$ git commit --message "Initial work on an R string calculator"
[master c85a965] Initial work on an R string calculator
 1 file changed, 12 insertions(+)
 create mode 100644 strcalc.R
{% endhighlight %}

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
valid result. Instead, R provides a handy value for missing or invalid data:
`NA`.

Edit your file so that, if the operator is not recognised, it returns `NA`
instead of 0.

~~~R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  if (operator == '+') {
    return(num0 + num1)
  } else {
    print('unknown operator!')
    return(NA)
  }
}
~~~

As before, we can check the status:

{% highlight console %}
$ git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   strcalc.R

no changes added to commit (use "git add" and/or "git commit -a")
{% endhighlight %}

This time, the output is a bit different: rather than showing you an
"Untracked file", your changes to `strcalc.R` are now listed as "not staged
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
diff --git a/strcalc.R b/strcalc.R
index bf38946..1a0d609 100644
--- a/strcalc.R
+++ b/strcalc.R
@@ -7,6 +7,6 @@ compute = function(input_string) {
     return(num0 + num1)
   } else {
     print('unknown operator!')
-    return(0)
+    return(NA)
   }
 }
```

You can see that changes to a line are encoded in git as deletion of
that line and addition of the modified line. Git works on a line by line
basis, which is why it's perfect for code and plain text files, but not
so great for big binary files such as images.

At any rate, the list of all the changes is indeed what we want to commit,
so we can use `--all`:

{% highlight console %}
$ git commit --all --message "Return NA, not 0, on invalid input"
{% endhighlight %}

Finally, let's add support for subtraction:

~~~R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  if (operator == '+') {
    return(num0 + num1)
  } else if (operator == '-') {
    return(num0 - num1)
  } else {
    print('unknown operator!')
    return(NA)
  }
}
~~~

Let's commit the changes:

{% highlight console %}
$ git add strcalc.R
$ git commit -m "Add support for subtraction"
{% endhighlight %}

(`-m` is a shortcut for `--message`.)

You now have a *history* that you can look at and interact with:

{% highlight console %}
$ git log
commit dbf8ab9aa4ed7e62e856c8c0620d2e2406baec01
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Mon Apr 11 21:28:47 2016 +1000

    Add support for subtraction

commit c0a11b0e00c591f51c6be601d25bbe8cdd6277ac
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Mon Apr 11 21:17:47 2016 +1000

    Return NA, not 0, on invalid input

commit c85a9652b60583e43e189f5c0acc4759453f0569
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Mon Apr 11 21:10:12 2016 +1000

    Initial work on an R string calculator

commit 8ab045720aed3f9478d6a580e5d9ba99071789f2
Author: Juan Nunez-Iglesias <jni.soma@gmail.com>
Date:   Mon Apr 11 12:19:18 2016 +1000

    Initial commit
{% endhighlight %}

You can *check out* earlier versions of your code using the hash of
a particular snapshot, like so:

{% highlight console %}
$ git checkout c85a9652b60583e43e189f5c0acc4759453f0569
Note: checking out 'c85a9652b60583e43e189f5c0acc4759453f0569'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at c85a965... Initial work on an R string calculator
{% endhighlight %}

You can verify that the file in your directory is now the older
version:

{% highlight console %}
$ cat strcalc.R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  if (operator == '+') {
    return(num0 + num1)
  } else {
    print('unknown operator!')
    return(0)
  }
}
{% endhighlight %}

> Note: you can use just the first few digits of the hash when checking
> out a specific revision:
> 
> {% highlight console %}
> $ git checkout c0a11
> Previous HEAD position was c85a965... Initial work on an R string calculator
> HEAD is now at c0a11b0... Return NA, not 0, on invalid input
> {% endhighlight %}

Thankfully, it's easy to go back to the latest version:

{% highlight console %}
$ git checkout master
Previous HEAD position was c0a11b0... Return NA, not 0, on invalid input
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 3 commits.
  (use "git push" to publish your local commits)
{% endhighlight %}

## Exercise 2: branches

Now we will undertake a major change to the structure of the function.
Because it's such a big
change, you want to work in a different _branch_ from "master", so that
you can keep using that one while fixing up the new stuff. In practice,
almost _every_ change is significant enough to warrant a new branch,
because "sprouting" one is cheap and easy.

{% highlight console %}
$ git checkout -b use-switch  # make branch to use switch statement
{% endhighlight %}

You should read that as: check out the current branch into a new branch
called `use-switch`. This is a good opportunity to learn about aliases,
which are short names for commands that make them more memorable:

{% highlight console %}
$ git config --global alias.sprout 'checkout -b'
{% endhighlight %}

You can then replace the previous command with:

{% highlight console %}
$ git sprout use-switch
{% endhighlight %}

Feel free to get as creative as you want with your alias names! They are
essential to making a more intuitive git interface that works for you. (But
do keep in mind that they will not be there when you use a different
computer!)

Now let's replace the longish list of if / else-if with a switch statement:

~~~R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  result = switch(operator,
                  '+' = num0 + num1,
                  '-' = num0 - num1)
  return(result)
}
~~~

Note that we've removed the informative error message when an invalid
operator was passed, but don't worry, we'll get to it!

Commit that change:

{% highlight console %}
$ git add strcalc.R
$ git commit -m "Use fancy-shmancy switch function"
{% endhighlight %}

... Oops! Your supervisor wants a working version of the program *now*,
and she has no patience for programs that have no error messages! Or
functions without documentation! Let's go back to our working version:

```console
$ git checkout master
```

And edit the file to add an informative comment under the function
definition:

```R
compute = function(input_string) {
  # Perform simple arithmetic encoded in input string
  # e.g. '1 + 2' -> 3, or '1 - 2' -> -1
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  if (operator == '+') {
    return(num0 + num1)
  } else if (operator == '-') {
    return(num0 - num1)
  } else {
    print('unknown operator!')
    return(NA)
  }
}
```

We can commit those changes:

```console
$ git commit -a -m "Add function documentation"
[master ef26741] Add function documentation
 1 file changed, 2 insertions(+)
```

Whew! Now that we've put out that fire, we can go back to our much more
beautiful switch version:

```console
$ git checkout use-switch
```

Now, `switch` lets us specify a default value at the very end, which is
equivalent to the `else` that we had for unknown operators in the original
function. So:

```R
compute = function(input_string) {
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  result = switch(operator,
                  '+' = num0 + num1,
                  '-' = num0 - num1,
                  NA)
  if (is.na(result)) {
    print('unknown operator!')
  }
  return(result)
}
```

And we commit it:

```console
$ git commit -a -m "Add NA processing for switch statement"
[use-switch 7a1e58b] Add NA processing for switch statement
 1 file changed, 5 insertions(+), 1 deletion(-)
```

Now you decide that your `use-switch` branch is ready to become the main
branch of your program. It's great because adding new operators will be
super-easy!

`git merge` can often automatically reconcile changes in two branched
histories:

```console
$ git checkout master
Switched to branch 'master'
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)
$ git merge use-switch
Auto-merging strcalc.R
Merge made by the 'recursive' strategy.
 strcalc.R | 14 +++++++-------
 1 file changed, 7 insertions(+), 7 deletions(-)
```

Git has automatically done the work of resolving the different changes!

```
compute = function(input_string) {
  # Perform simple arithmetic encoded in input string
  # e.g. '1 + 2' -> 3, or '1 - 2' -> -1
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  result = switch(operator,
                  '+' = num0 + num1,
                  '-' = num0 - num1,
                  NA)
  if (is.na(result)) {
    print('unknown operator!')
  }
  return(result)
}
```


## Exercise 3: merge conflicts

Sometimes, it's not clear *how* two branching changes should be merged, for
example if the same line is changed in both histories. Which change should
take precedence? Git does the right thing and defers the decision to you.
Let's see how it does this.

Create a branch to add the multiplication operator:

```console
$ git checkout master -b add-multiplication
```

Thanks to our work with `switch`, it's as simple as adding the following
line after the subtraction operator:

```R
                  '*' = num0 * num1,
```

Commit that change:

```console
$ git commit -a -m "Add support for multiplication operator"
```

Now, repeat the same procedure, including the new branch, for division:

```console
$ git checkout master -b add-division
# ... make changes to R file ...
$ git commit -a -m "Add support for division operator"
```

Finally, bring those changes into the master branch:

{% highlight console %}
$ git checkout master
$ git merge add-multiplication
$ git merge add-division
{% endhighlight %}

At this point git will, to use a technical term, *chuck a hissy fit*, and
refuse to perform the merge:

{% highlight console %}
Auto-merging strcalc.R
CONFLICT (content): Merge conflict in strcalc.R
Automatic merge failed; fix conflicts and then commit the result.
{% endhighlight %}

We must go into the file and manually fix the conflicting
changes. Git places markers on the file where it has found conflicts,
so you can quickly identify those locations and decide on a fix:

~~~
compute = function(input_string) {
  # Perform simple arithmetic encoded in input string
  # e.g. '1 + 2' -> 3, or '1 - 2' -> -1
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  result = switch(operator,
                  '+' = num0 + num1,
                  '-' = num0 - num1,
<<<<<<< HEAD
                  '*' = num0 * num1,
=======
                  '/' = num0 / num1,
>>>>>>> add-division
                  NA)
  if (is.na(result)) {
    print('unknown operator!')
  }
  return(result)
}
~~~

Git is saying that in the current branch ("HEAD"), the file at that
position contains the single line, `'*' = num0 * num1,`, while the
`add-division` branch, which we are trying to merge, contains the line,
`'/' = num0 / num1`, *at the very same spot*.

You fix merge conflicts by navigating to the `<<<<<<<` markers, comparing
the two conflicting versions, and leaving the file as you want it to be
committed: removing the markers, and combining the changes in a way
that makes sense, and that a system like git can't figure out by itself.

In this case, we simply remove all the markers, and leave the two lines
one after the other inside the file.

~~~R
compute = function(input_string) {
  # Perform simple arithmetic encoded in input string
  # e.g. '1 + 2' -> 3, or '1 - 2' -> -1
  values = unlist(strsplit(input_string, ' '))
  num0 = as.integer(values[1])
  operator = values[2]
  num1 = as.integer(values[3])
  result = switch(operator,
                  '+' = num0 + num1,
                  '-' = num0 - num1,
                  '*' = num0 * num1,
                  '/' = num0 / num1,
                  NA)
  if (is.na(result)) {
    print('unknown operator!')
  }
  return(result)
}
~~~

Finally, tell git you've fixed the problem by `git add`ing the file,
commit, and the merge will complete!

{% highlight console %}
$ git add strcalc.R
$ git commit
{% endhighlight %}
