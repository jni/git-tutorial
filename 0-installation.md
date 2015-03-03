---
layout: page
permalink: installation/
title: Installation and setup
---

To work with this tutorial, you're going to need a few things:

- **Git**, of course. Install this by going to the git homepage,
  [git-scm.com](http://git-scm.com). On Linux, you probably already have git,
  or you can install it with `sudo apt-get install git-all` or
  `sudo yum install git`.
- **A graphical git client or browser**. This let you visualise your git
  history more easily, and understand the concepts behind git better. For a
  full list of clients, see [here](http://git-scm.com/downloads/guis). On Mac,
  I recommend GitX and GitHub for Mac (both).
- **A text editor**. Try [Sublime Text](http://www.sublimetext.com)
  or [Atom](https://atom.io) (both of which are multiplatform). Configure them
  to be your default git editors by following the instructions on
  [this page](https://help.github.com/articles/associating-text-editors-with-git/).
  Note: programs like Microsoft Word or TextEdit are *not* valid text editors
  here because they don't produce plain text files, but rather more elaborate
  file formats that include text formatting information.
- **A GitHub account**. Create an account by going to
  [github.com](https://github.com).
- **SSH keys to access GitHub**. Without these, you will need to type your
  GitHub password every single time you try to do read from or write to your
  GitHub account. (Which will be many, many time! ;) Follow the instructions
  [here](https://help.github.com/articles/generating-ssh-keys/), making sure
  that you are seeing the instructions for your OS (Mac, Windows, or Linux).

Additionally, the tutorial is going to be led in Python. If you're familiar
with a different programming language, feel free to convert what we write to
that language! At the start of the tutorial, we'll try to pair up by
programming language preference.

For those that don't have a preference, you probably want to have Python
installed. On Mac and Linux, everything should work out of the box, but for
Windows, you'll want to download the
[Anaconda Python distribution](http://continuum.io/downloads). (You probably
want to download it no matter what your OS! It's awesome!)

## Setup

Additionally, you'll want to set up git so that it knows your full name and
email address. Fire up a console/terminal, and type:

```console
$ git config --global user.name "Your Name"
$ git config --global user.email your.name@email.com
```

(Use the same email you used for your GitHub account.)

The following command also lets you see a rudimentary graphic of your history
without needing a GUI git client:

```console
$ git config --global alias.lsd "log --graph --decorate --pretty=oneline --abbrev-commit --all"
```

---

Whew! That's quite a lot of stuff! But I hope by the end of the tutorial you'll
find it all useful and worth getting! (Plus: free stuff!)