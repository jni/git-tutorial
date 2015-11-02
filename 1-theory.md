---
layout: page
permalink: git-theory/
title: Git theory
---

Git stores the complete history of your files for a project in a special
directory (a.k.a. a folder) called a *repository*, or repo.
This repo is usually in a hidden folder called `.git` sitting next to your
files.

Keeping an entire history sounds expensive, but it isn't, because git is smart
and does not replicate all your files, and instead keeps only *changes* to your
files.

How does a repo work?

(Don't stress this section too much: it will all become second nature after
you've used git for a bit.)

## Hashes and diffs

A *hash* is 160-bit summary of your data. Using an algorithm called SHA-1, Git
mashes up the contents of your files until only 160 bits remain[^hash][^hash2].
SHA1 and related algorithms have the nice property that if your files change
_just a little bit_, their hash changes _dramatically_. For example, here are
the SHA1 hashes of files containing just the string "hello" or "Hello":

{% highlight bash %}
$ echo "hello" | openssl dgst -sha1
f572d396fae9206628714fb2ce00f72e94f2258f
$ echo "Hello" | openssl dgst -sha1
1d229271928d3f9e2bb0375bd6ce5db6c6d348d9
{% endhighlight %}

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

~~~
$ git checkout <hash>
~~~

This finds the version matching the supplied hash in the `.git` directory, and
then copies it out to your working directory.

## Branches and tags

Hashes are unwieldy and difficult to read, so git offers a number of
conveniences to avoid having to deal with them most of the time. The first is
_tags_, which are simply alternate names for specific hashes. Thus, if you are
writing a paper, you can tag the version you submit as follows:

~~~
$ git tag submitted-version
~~~

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
_parent_ commits that are also hashed, it is easy to reason about
duplicated versions of the history.

In addition to enabling backups, this also helps with collaboration, but adds
an extra layer that you need to consider when using git: in addition to your
repository in `.git` and your working copy, there are also "clone"
repositories, also called "forks". These are useful for backups and to
collaborate with others.

While we *commit to* a repo from a working copy, and we *check out* to a
working copy from a repo, we *push to* a fork from our repo, or *fetch from* a
fork to our repo.

# References

[^hash]: http//alblue.bandlem.com/2011/08/git-tip-of-week-objects.html
    A technical description of git hashing.

[^hash2]: http://www.ericsink.com/vcbe/html/cryptographic_hashes.html An
    introduction to cryptographic hashing
