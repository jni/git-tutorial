---
layout: page
permalink: working-with-github/
title: Working with GitHub
---

From earlier:

> Git is a tool to control revisions. GitHub is a web service centered
> on git. It provides repository hosting as well as a suite of
> workflows to aid collaboration.

In this section you will learn about *remote repositories*, which are
histories that are not next to your working directory. The operations
involved in using these include pushing, pulling, forking, and pull
requests.

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

{% highlight console %}
$ git remote
origin
{% endhighlight %}

Git is saying that it knows about a single remote repository, called
origin. To see what it knows *about* this remote, use the `-v` (verbose)
flag:

{% highlight console %}
$ git remote -v
origin	git@github.com:jni/planets.git (fetch)
origin	git@github.com:jni/planets.git (push)
{% endhighlight %}

That tells both the name of the remote, *and its location*. "origin" is
the default name for the remote from which you cloned the current
repository.

Now, push your local changes back to the origin:

{% highlight console %}
$ git push origin master:master
{% endhighlight %}

Read the above as "push to "origin" my branch "master" onto its branch
"master".

After this, you'll be able to refresh your repo page on GitHub and
browse your code's history.

## Exercise 4: GitHub pull requests

For this exercise you will have to pair up with your neighbour, which
we will name Alice. (And your name is Bob, in keeping with the computer
science literature.)

Delete your "planets" repository on GitHub (this is done under "Settings"
in the right-hand menu). You've realised that Alice has her own and
that you can both save effort by collaborating on this project. You, being
human, want to make sure that concerns for human crew members are noted.

Navigate to Alice's repository on GitHub
(https://github.com/[Alice's username]/planets), and click the "Fork" button.
This will create a copy of Alice's repo on your GitHub account, which
you can then clone on your machine as before. But note that you need to
delete your existing repository! Instructions below (some of the directories
and obviously the "bob" username needs to be changed to yours!):

{% highlight console %}
$ pwd
/Users/bob/projects/planets
$ cd ..
$ rm -rf planets
$ git clone git@github.com:bob/planets.git
$ cd planets
$ git checkout -b humans
{% endhighlight %}

Edit the `mars.txt` file to look like so:

~~~
# Mars

## Properties pertaining to monster habitability

Mars is cold and dry, but everything is Dracula's favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability

We will need to make our own oxygen.
~~~

Now commit those changes and push them to a new branch on GitHub. If you use
the `--set-upstream` function, you tell git to create a branch with the same
name that "tracks" the current branch. This makes future pushes easier.

{% highlight console %}
$ git add mars.txt
$ git commit -m "Add human concerns about oxygen"
$ git push origin --set-upstream humans
{% endhighlight %}

Go to the GitHub page for the project. You should see a new button
showing that you've recently updated a branch and prompting you to
*initiate a pull request*.

Here's how this works: you don't know Alice. You probably have never met
her. So it's natural that you can't just push random stuff willy-nilly to her
repository. However, you do have a *shared history*, because you *forked* hers,
and *she* has access to your new changes. So, instead of *pushing* your
changes, you ask *her* to *pull* from your own history.

The PR will tell Alice that you've made some
changes to the code and you would like her to incorporate them into
her project. Notice that you did this *without needing any special
access from Alice!* This is the magic of GitHub.

![GitHub's impact on FOSS](/images/gh.png)

Click on the PR button and fill in the form. Filling in a useful
title and message here is very important!

> ### Pull request etiquette
> 
> Make sure your title and description are informative. 99% of the time, when
> you make a pull request (PR), the person on the other end is very busy, knows
> no background about the PR and doesn't understand why you made the changes
> you made. In general, the onus is on the requester to comply with all the
> repository's formatting guidelines and so forth. By convention, many
> repositories have a `CONTRIBUTING.txt` file explaining the contribution
> process. If it's there, be sure to read it before submitting a PR!
> 
> When in Rome, do as the Romans do. Look at their existing codebase
> and try to follow their example. (This is not to say that you can't
> improve on it; but make sure your documentation and testing *at least*
> least meets their standards.)



Alice should get an email notification that there is a pull request to
her project. Clicking on it, she will be taken to the web form for the
PR, where she can examine the changes that Bob has made (the "Files changed"
tab).

Alice will note that this sentence would be made much more useful if it
included some guidance about *how* to make oxygen on Mars. She comments on the
PR page: "Thanks! Could you add a line about how this could be achieved?"

After some web searching, Bob discovers that Mars has plenty of carbon dioxyde,
so he suggests extracting the oxygen from that.

~~~
# Mars

## Properties pertaining to monster habitability

Mars is cold and dry, but everything is Dracula's favourite colour.
The two moons may be a problem for Wolfman,
but the Mummy will appreciate the lack of humidity.

## Properties pertaining to human habitability

We will need to make our own oxygen.
We can extract it from CO2, which is plentiful on Mars.
~~~

Again, Bob commits and pushes his changes:

{% highlight console %}
$ git add mars.txt
$ git commit -m "Specify how to obtain oxygen on Mars"
$ git push  # no need to specify repo or branch anymore, having `set-upstream`
{% endhighlight %}

If both Bob and Alice go back to the PR page, they will see that the PR has
been automagically updated with Bob's new changes! (Though they may need to
refresh the page.)

Alice, satisfied with the update, can now click on the "Merge pull
request" button and incorporate Bob's changes to her code!

One last thing needs to happen to really synchronise everyone's histories.
Although Alice has Bob's changes, *Bob doesn't have Alice's commit
incorporating his changes.* If he continues to work on his `humans` branch,
their histories will diverge. And if he works on his `master` branch, his
changes won't be there!

The solution is for him to *pull* from *Alice's* repository. For this, he needs
to add it to his list of remotes (remember remotes?):

{% highlight console %}
$ git remote -v
origin	git@github.com:bob/planets.git (fetch)
origin	git@github.com:bob/planets.git (push)
$ git remote add upstream git@github.com:alice/planets.git
$ git remote -v
origin	git@github.com:bob/planets.git (fetch)
origin	git@github.com:bob/planets.git (push)
upstream	git@github.com:alice/planets.git (fetch)
upstream	git@github.com:alice/planets.git (push)
$ git checkout master
$ git pull upstream master  # get upstream's master branch, and merge
{% endhighlight %}

Bob can now inspect his history log and see that both his changes and Alice's
merge are there:

{% highlight console %}
$ git lsd
*   3fb6c83 (HEAD, master) Merge pull request #1 from bob/humans
|\  
| * c00e290 (humans) Specify how to obtain oxygen on Mars
| * 49ad7b0 Add human concerns about oxygen
|/  
*   1fb7977 Merge branch 'pedant'
|\  
| * 50726c3 (pedant) Complete first sentence
* | 37d72a7 (third-person) Change Dracula reference to the third person
|/  
*   2905d8d Merge branch 'headings'
|\  
| * c932703 (headings) Add heading for human-specific concerns
| * 14dde3e Add title and heading
* | 730d526 Improve English style by replacing 2 with two
|/  
* 37aa3b5 Note Mummy's preference for Martian atmosphere
* 7126926 Add concerns about effect of moons on Wolfman
* 3e1d628 Start notes on Mars as a base
* 2407f61 (origin/master, origin/HEAD) Initial commit
{% endhighlight %}

And all his branches that have been merged back can be safely deleted:

{% highlight console %}
$ git branch -d humans pedant third-person headings
{% endhighlight %}

## Bonus exercise 1

Do the reverse approach, which is a bit different. Alice has gained a
collaborator in Bob. Even though she still maintains control of the project
repository, she wants to enlist his help. She sprouts a branch, adds a line or
two (at the risk of mixing metaphors, an example:
Medusa might appreciate the lack of reflective surfaces), then
*creates a PR against her own repository.* She then asks Bob to review her
change by mentioning his username (e.g. `@bob`, as in Twitter) in a comment on
the PR page. Only when Bob gives his ok (or perhaps he spots a typo) does she
merge.

This practice of requesting code reviews for pull requests, even when you
control the repository, is universal among programming teams, because it
dramatically improves the quality of the code. Two pairs of eyes are more than
twice as effective as one pair.

## Bonus exercise 2

Bob pulls Alice's latest changes. He then works on a new section about
*agriculture on Mars*. (e.g. Dracula vetoes any wishes to bring garlic, while
Wolfman insists on taking a flock of sheep.) He submits his pull request.

Meanwhile, Alice makes her own
changes, adding a (blank) section on rocket fuel requirements.

Bob's pull request now shows a greyed out "Merge" button, because there are
merge conflicts! Unlike before, you don't have access to the history on GitHub,
so you can't just proceed with the merge and fix the merge conflicts.

The common solution is to *rebase*, that is, to replay the changes on Bob's
branch on top of the latest `master` from Alice's repository.

{% highlight console %}
$ git checkout master
$ git pull upstream master
$ git rebase master agriculture
{% endhighlight %}

During the rebase, Bob will have merge conflicts. He needs to fix these as
before, `git add` the file, and then `git rebase --continue` to complete the
rebase.

Finally, Bob will try to push back to his GitHub account but this will fail.
(Why?) The solution is to use `git push --force-with-lease`.

## Notes on why you should make your own code open source

- The idea that you will be scooped by someone looking at your code is
  ludicrous. Reading code is *hard work* and no one will do it on the
  off chance that there is a Nature paper buried in there.
- Your own coding habits and practice will improve just by knowing that
  it's out there. (Shame is a powerful motivator! =P)
- If someone *does* look at your code and finds it useful, chances are
  that you will have gained a new collaborator, not a competitor.
- It is just good scientific practice!
