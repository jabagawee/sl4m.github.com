---
layout: post
title: git branching and merging
date: 2010-06-28 10:30:00 -05:00
categories:
  -- git
  -- github
---

As I aim to become better at using git, I thought I'd share how to branch, then later merge the branch on a git repo.  I wanted to do this for my Tic Tac Toe project because I was going to add the Limelight UI.  It's actually fairly easy to branch and merge.  I used [Scott Chacon's](http://twitter.com/chacon) wonderful [Git Reference website](http://gitref.org/) and followed his examples.

## Listing current branches

Note: I'm using zsh and these [dot files](http://github.com/jferris/config_files).

To list local branches:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git branch
* master
</code></pre>

To list remote branches:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git branch -r
  origin/HEAD -> origin/master
  origin/master
</code></pre>

## Create a new branch, check out new branch

Here I'm going to create a branch called limelight.

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git branch limelight
[master][~/local/git/tic_tac_toe_ruby] git branch
  limelight
* master
</code></pre>

You might be wondering what the asterisk next to the branch name means, in this case, the asterisk next to "master".  It basically tells you which branch you checked out.  You might then be wondering what "checked out" means.  When you have a branch checked out, it is the active branch you are working in.  *git checkout* command allows you to switch between the local branches available.  git is smart enough to understand which folder/files are in which branch, so you do not need to create separate directories for each branch.  All local branches live in the same directory!  Brilliant!

So to check out the new branch (limelight), simply run this command:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git checkout limelight
Switched to branch 'limelight'
</code></pre>

You could easily kill two birds with one stone and create a branch and check it out with a single command:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git checkout -b limelight
Switched to a new branch 'limelight'
</code></pre>

Or kill four birds with two stones: create a remote branch, create a local branch, track the local branch with a remote branch and check out the new local branch.

This creates a copy of origin into a new remote branch:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git push origin origin:refs/heads/limelight
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:sl4m/tic_tic_toe_ruby.git
 * [new branch]      origin/HEAD -> limelight

[master][~/local/git/tic_tac_toe_ruby] git branch -a
master
remotes/origin/HEAD -> origin/master
remotes/origin/limelight
remotes/origin/master
</code></pre>

This creates a local branch, tracks it with the newly created remote branch, and checks it out:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git checkout --track -b limelight origin/limelight
Branch limelight set up to track remote branch limelight from origin.
Switched to a new branch 'limelight'
</code></pre>

Go have at it and start making changes to the new branch.  Once you're finished and you want to merge your changes in this branch to, let's say, master, you'll need to use *git merge*.

## Merging a branch to another

*git merge* is super smart about detecting folder/file creates/deletes as well as modifications to existing folders/files.  One of the only times where it may not quite understand what you want is when you make modifications of the same folder/file on both branches.  You'll run into merge conflicts which you can read more about [here](http://gitref.org/branching/#merge).

To merge a branch (limelight) to another (master), check out master and run *git merge*:

<pre><code class="no-highlight">
[limelight][~/local/git/tic_tac_toe_ruby] git checkout master
Switched to branch 'master'
[master][~/local/git/tic_tac_toe_ruby] git merge limelight
Updating 2083092..b7af83b
Fast-forward
 default_scene/players/combo_box.rb     |    6 --
 default_scene/players/default_scene.rb |  107 +++++++++++++++++++++++++++++++-
 default_scene/players/menu_item.rb     |    1 +
 default_scene/players/square.rb        |   35 +++++++++--
 default_scene/props.rb                 |   34 ++++++-----
 default_scene/styles.rb                |   23 +++++--
 lib/cpu_player.rb                      |    4 +-
 lib/game.rb                            |    1 +
 lib/human_player.rb                    |    5 +-
 lib/min_max_player.rb                  |   18 +++---
 lib/std_ui.rb                          |    5 ++
 lib/tic_tac_toe.rb                     |    2 +-
 production.rb                          |   18 +++---
 spec/default_scene_spec.rb             |   95 +++++++++++++++++++++++++---
 spec/menu_item_spec.rb                 |   24 +++++++
 spec/production_spec.rb                |    7 +--
 spec/square_spec.rb                    |   38 +++++++++++
 17 files changed, 352 insertions(+), 71 deletions(-)
 delete mode 100644 default_scene/players/combo_box.rb
 create mode 100644 spec/menu_item_spec.rb
 create mode 100644 spec/square_spec.rb
</code></pre>

Now you should see the changes you made in the new branch in the branch you merged your changes.  In this case, I see the changes I made in limelight in master.

Hold your horses, we're done yet.  If you run *git status*, you'll see the merge took place locally, but not on your remote branches (see your remote repo).

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git status
# On branch master
# Your branch is ahead of 'origin/master' by 4 commits.
#
nothing to commit (working directory clean)
</code></pre>

If you notice above, it says "your branch is ahead of 'origin/master' by 4 commits".  If you've worked with git to where you committed multiple times, but not pushed your commits to your remote repo, you will see "your branch is ahead" message when running *git status*.  So we'll need to push the commits to the remote repo.

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git push origin master
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:sl4m/tic_tac_toe_ruby.git
   2083092..b7af83b  master -> master
</code></pre>

Now you'll see the merged changes in your remote repo.

## Delete a local branch and then remote branch

Finally, and optionally, you can delete your local branch if you don't need it anymore.  You can also make the change reflect on your remote repo.

To delete your local branch:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git branch -d limelight
Deleted branch limelight (was b7af83b).
</code></pre>

To delete your remote branch:

<pre><code class="no-highlight">
[master][~/local/git/tic_tac_toe_ruby] git push origin :limelight
To git@github.com:sl4m/tic_tac_toe_ruby.git
 - [deleted]         limelight
[master][~/local/git/tic_tac_toe_ruby] git branch
* master
[master][~/local/git/tic_tac_toe_ruby] git branch -r
  origin/HEAD -> origin/master
  origin/master
</code></pre>

Make sure to be in a different branch than the one you're about to delete.  Otherwise, git will bark at you.

<pre><code class="no-highlight">
[limelight][~/local/git/tic_tac_toe_ruby] git branch -d limelight
error: Cannot delete the branch 'limelight' which you are currently on.
</code></pre>

Hope this helped.  I encourage you to start branching!
