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
> 
> I'm a big proponent of BSD. Jake Vanderplas, Director of Research at
> the University of Washington's eScience Institute, has a fantastic
> [blog post](http://www.astrobetter.com/blog/2014/03/10/the-whys-and-hows-of-licensing-scientific-code/)
> explaining why.

Follow [GitHub's
instructions](https://help.github.com/articles/creating-a-new-repository)
to create a new repository called "planets", with the description,
"Notes on suitability of various planets for a crewed mission."

Once created, copy the URL from the box in the bottom right, and open
a terminal window to *clone* the project from GitHub to your local
machine.
(Note: "jni" should be replaced by your own username.)

{% highlight console %}
$ git clone git@github.com:jni/planets.git
$ cd planets
{% endhighlight %}

You can type `git status` to see that there is a git-tracked project there,
but nothing to add to the history:

{% highlight console %}
$ git status
{% endhighlight %}

Now you can start working on the project. We are going to add notes about
Mars.

In your editor, type:

~~~
Cold and dry, but everything is my favourite colour.
~~~

And save it to a file called `mars.txt` in the `planets` directory.

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

        mars.txt

nothing added to commit but untracked files present (use "git add" to track)
{% endhighlight %}

Tell git to add the file to its tracking system:

{% highlight console %}
$ git add mars.txt
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        new file:   mars.txt
{% endhighlight %}

Then, *commit* the changes to the history:

{% highlight console %}
$ git commit --message "Start notes on Mars as a base"
[master 3e1d628] Start notes on Mars as a base
 1 file changed, 1 insertion(+)
 create mode 100644 mars.txt
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

Add another line, to the file, so that it reads as follows:

~~~
Cold and dry, but everything is my favourite colour.
The 2 moons may be a problem for Wolfman.
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

    modified:   mars.txt

no changes added to commit (use "git add" and/or "git commit -a")
{% endhighlight %}

This time, the output is a bit different: rather than showing you an
"Untracked file", your changes to `mars.txt` are now listed as "not staged
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

{% highlight console %}
$ git commit --all --message "Add concerns about effect of moons on Wolfman"
{% endhighlight %}

Finally, let's add a line about the Mummy's preferences:

~~~
Cold and dry, but everything is my favourite colour.
The 2 moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.
~~~

(Note the comma at the end of the second line.)

In addition to producing the `git status` one-line summary that we've
seen before, git can produce a line-by-line description of everything
that's changed since our last commit:

{% highlight console %}
$ git diff
diff --git a/mars.txt b/mars.txt
index 30c4e2f..86cd5c2 100644
--- a/mars.txt
+++ b/mars.txt
@@ -1,2 +1,3 @@
 Cold and dry, but everything is my favourite colour.
-The 2 moons may be a problem for Wolfman.
+The 2 moons may be a problem for Wolfman,
+but the Mummy will appreciate the lack of humidity.
{% endhighlight %}

That's a machine-readable "diff format" that can be used by git and other
software to reproduce the changes. Notice that a substitution (changing a
full-stop into a comma) is encoded as a line deletion and addition, and the
new line about the Mummy is another addition.

Let's commit the changes:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Note Mummy's preference for Martian atmosphere"
{% endhighlight %}

(`-m` is a shortcut for `--message`.)

You now have a *history* that you can look at and interact with:

{% highlight console %}
$ git log
commit 37aa3b547fde90c36af10c45b07ca74c6936750d
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Tue May 26 15:04:59 2015 +1000

    Note Mummy's preference for Martian atmosphere

commit 71269262f49859b9b53b2e3ccbeec084fcae624b
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Tue May 26 14:49:15 2015 +1000

    Add concerns about effect of moons on Wolfman

commit 3e1d628655c096b98d0fe7a2994350c014196714
Author: Juan Nunez-Iglesias <juan.n@unimelb.edu.au>
Date:   Tue May 26 12:46:17 2015 +1000

    Start notes on Mars as a base

commit 2407f61e7bddda4e59118f2dfe78317a1d5a199c
Author: Juan Nunez-Iglesias <jni.soma@gmail.com>
Date:   Tue May 26 12:23:56 2015 +1000

    Initial commit
{% endhighlight %}

You can *check out* earlier versions of your code using the hash of
a particular snapshot, like so:

{% highlight console %}
$ git checkout 71269262f49859b9b53b2e3ccbeec084fcae624b
Note: checking out '71269262f49859b9b53b2e3ccbeec084fcae624b'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name

HEAD is now at 7126926... Add concerns about effect of moons on Wolfman
{% endhighlight %}

You can verify that the file in your directory is now the older
version:

{% highlight console %}
$ cat mars.txt
Cold and dry, but everything is my favourite colour.
The 2 moons may be a problem for Wolfman.
{% endhighlight %}

> Note: you can use just the first few digits of the hash when checking
> out a specific revision:
> 
> {% highlight console %}
> $ git checkout 7126
> HEAD is now at 7126926... Add concerns about effect of moons on Wolfman
> {% endhighlight %}

Thankfully, it's easy to go back to the latest version:

{% highlight console %}
$ git checkout master
{% endhighlight %}

## Exercise 2: branches

Now we will undertake a major change to the structure of the document.
Because it's such a big
change, you want to work in a different _branch_ from "master", so that
you can keep using that one while fixing up the new stuff. In practice,
almost _every_ change is significant enough to warrant a new branch,
because "sprouting" one is cheap and easy.

{% highlight console %}
$ git checkout -b headings  # make branch to add headings
{% endhighlight %}

You should read that as: check out the current branch into a new branch
called `headings`. This is a good opportunity to learn about aliases,
which are short names for commands that make them more memorable:

{% highlight console %}
$ git config --global alias.sprout 'checkout -b'
{% endhighlight %}

You can then replace the previous command with:

{% highlight console %}
$ git sprout headings
{% endhighlight %}

Feel free to get as creative as you want with your alias names! They are
essential to making a more intuitive git interface that works for you. (But
do keep in mind that they will not be there when you use a different
computer!)

Now let's change the style of the text to be a bit less robotic:

~~~
# Mars

## Properties pertaining to monster habitability

Cold and dry, but everything is my favourite colour.
The 2 moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.
~~~

Commit that change:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Add title and heading"
{% endhighlight %}

... and add a heading for human-focused concerns:

~~~
# Mars

## Properties pertaining to monster habitability

Cold and dry, but everything is my favourite colour.
The 2 moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

Now commit, return to the master branch, and make some unrelated changes:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Add heading for human-specific concerns"
$ git checkout master
{% endhighlight %}

Replace the number "2" with the word "two":

~~~
Cold and dry, but everything is my favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.
~~~

And commit those changes:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Improve English style by replacing 2 with two"
{% endhighlight %}

Now, you've thought about the "headings" branch and decide it's time to
merge those changes back into your "main" history. `git merge` intelligently
merges two history branches:

{% highlight console %}
$ git merge headings  # merge branch 'headings' into current branch ('master')
{% endhighlight %}

Git automatically generates a log message and produces the merged file:

~~~
# Mars

## Properties pertaining to monster habitability

Cold and dry, but everything is my favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

Easy!

## Exercise 3: merge conflicts

Sometimes, changes in a branch and changes in the master branch are too
difficult for git to merge automatically, and `git merge` will result in a
*merge conflict*. In this exercise, you will see how to deal with those.

Dracula has been asked to write in the third person because the document
must make sense independently of knowing the author. So he needs to make a
branch to make this change:

{% highlight console %}
$ git checkout -b third-person
{% endhighlight %}

(Use your `sprout` alias if you made one! I leave the "canonical form" here
for those that haven't, or that mysteriously skipped the alias section.)

~~~
# Mars

## Properties pertaining to monster habitability

Cold and dry, but everything is Dracula's favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

Commit the change:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Change Dracula reference to the third person"
{% endhighlight %}

Now, a pedantic member of the team (let's call them "Juan Nunez-Iglesias")
has requested that only valid, complete English sentences should appear in
the document text. So we make another branch for that change.

{% highlight console %}
$ git checkout master
$ git checkout -b pedant
{% endhighlight %}

And edit the file as follows:

~~~
# Mars

## Properties pertaining to monster habitability

Mars is cold and dry, but everything is my favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

And commit our changes:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Complete first sentence"
{% endhighlight %}

Finally, we get back the master branch and try to merge each branch in
sequence:

{% highlight console %}
$ git checkout master
$ git merge third-person
$ git merge pedant
{% endhighlight %}

At this point git will, to use a technical term, *chuck a hissy fit*, and
refuse to perform the merge:

{% highlight console %}
Auto-merging mars.txt
CONFLICT (content): Merge conflict in mars.txt
Automatic merge failed; fix conflicts and then commit the result.
{% endhighlight %}

We must go into the file and manually fix the conflicting
changes. Git places markers on the file where it has found conflicts,
so you can quickly identify those locations and decide on a fix:

~~~
# Mars

## Properties pertaining to monster habitability

<<<<<<< HEAD
Cold and dry, but everything is Dracula's favourite colour.
=======
Mars is cold and dry, but everything is my favourite colour.
>>>>>>> pedant
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

Git is saying that in the current branch ("HEAD"), the file at that
position contains the single line, "Cold and dry, but everything is
Dracula's favourite colour.", while the `pedant` branch, which we
are trying to merge, contains the line, "Mars is cold and dry, but
everything is my favourite colour."

You fix merge conflicts by navigating to the `<<<<<<<` markers, comparing
the two conflicting versions, and leaving the file as you want it to be
committed: removing the markers, and combining the changes in a way
that makes sense, and that a system like git can't figure out by itself.

~~~
# Mars

## Properties pertaining to monster habitability

Mars is cold and dry, but everything is Dracula's favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability
~~~

Finally, tell git you've fixed the problem by `git add`ing the file,
commit, and the merge will complete!

{% highlight console %}
$ git add mars.txt
$ git commit
{% endhighlight %}
