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
- **A graphical git client or browser**. This lets you visualise your git
  history more easily, and understand the concepts behind git better. For a
  full list of clients, see [here](http://git-scm.com/downloads/guis). On Mac,
  I recommend GitX and GitHub for Mac (both). A friend really likes Git Tower.
  Finally, the new kid on the block is [gitup](http://gitup.co), which looks
  amazing.
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
  GitHub account. (Which will be many, many times! ;) Follow the instructions
  [here](https://help.github.com/articles/generating-ssh-keys/), making sure
  that you are seeing the instructions for your OS (Mac, Windows, or Linux).

  **For Windows users**: when typing a passphrase, it seems that the keyboard is not working. However, it is just a security feature to avoid showing it. Go ahead and type your passphrase, then repeat it when requested.

  Moreover, if you get the error:

  {% highlight console %}
  $ ssh-add ~/.ssh/id_rsa
  Could not open a connection to your authentication agent.
  {% endhighlight %}

  you will need to use this command to start the ssh-agent:

  {% highlight console %}
  eval `ssh-agent -s`
  {% endhighlight %}

  be careful to use the proper backtick symbol. Then type:

  {% highlight console %}
  ssh-add ~/.ssh/id_rsa
  {% endhighlight %}

  (change the filename from id_rsa according to the one you used). See [here](http://stackoverflow.com/a/17848593) for more info.



## Setup

Additionally, you'll want to set up git so that it knows your full name and
email address. Fire up a console/terminal, and type:

{% highlight console %}
$ git config --global user.name "Your Name"
$ git config --global user.email your.name@email.com
{% endhighlight %}

(Use the same email you used for your GitHub account.)

The following command also lets you see a rudimentary graphic of your history
without needing a GUI git client:

{% highlight console %}
$ git config --global alias.lsd "log --graph --decorate --pretty=oneline --abbrev-commit --all"
{% endhighlight %}

Then you can get a nice history *within your terminal* by typing:

{% highlight console %}
$ git lsd
{% endhighlight %}

---

Whew! That's quite a lot of stuff! But I hope by the end of the tutorial you'll
find it all useful and worth getting! (Plus: free stuff!)
