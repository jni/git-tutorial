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

Exercise: initialise a repo, make some changes, git add, git commit (x2). git checkout master -b mult; add multiplication in two commits. Then check out master, add usage string. Then git merge.

# Working with Github. semi-centralised model.
 - creating a new repository, choosing a license. BSD/GPL most popular, significant philosophical and practical differences.
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

