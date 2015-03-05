---
layout: default
title: Home
---

# <center> Open Source Science with Git and Github: a tutorial </center>

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
control, even if you are working alone. Systems such as git save you from this.

There are (at least) three stages of git enlightenment:

1. Maintain a linear history for a file or group of files.
2. Use branches effectively to group related changes together, as well as
   maintain parallel versions.
3. Use branches and pull-requests to collaborate effectively.

It took me a long time to pass all three, and I hope this tutorial will help
someone get there faster.

# Tutorial timeline (approximate)

0. Installing and setting up git, GitHub, and others (30min)
1. Git theory (30min)
2. Working with git (1h)
3. Working with GitHub (1h)
4. Advanced topics discussion (15min)
5. Putting it together: submit improvements to this workshop! (45min)

Click on the sections below to get started with git!

- [Installation and setup](installation/)
- [Git theory](git-theory/)
- [Working with git](working-with-git/)
- [Working with GitHub](working-with-github/)
- [Advanced topics](advanced-topics/)

## References

[^git]: http://git-scm.com/ The Git homepage. A plethora of resources.

[^gitbook]: http://git-scm.com/book The Git book. Your starting point for all
    git knowledge.
