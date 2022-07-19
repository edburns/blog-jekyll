---
layout: post
title:  "Using git worktree and a script to copy commits from one branch to another in the same repo"
date:   2022-07-19 09:25-0400
comments: true
---

This brief post shows how to use `git worktree` to perform development work multiple branches of your local clone of a remote git repository, such as a repository on GitHub.  
The `worktree` git sub-command allows you to "Manage multiple working trees". If you want to know everything there is to know about `worktree`, you can view the manual page by doing `man git-worktree`.  Yes, that's right, the literal string `git-worktree`.  All git sub-commands have their own manual page which you can access with `man git-subcommandname`.  Try it with `man git-branch`, for example.

The post assumes makes the following assumptions.

* You have already done `git clone` of the remote repository on your local environment.
* For discussion, this local clone is called `myrepo`.
* Within `myrepo` you already have three branches: `main`, `myfeature`, and `myotherfeature`.
* The `main` branch is currently checked out.  This is the default behavior when you do `git clone`.

### Check out myfeature using git worktree add

1. `cd myrepo`
1. `git status` <br />
   You should see the following.

   ```
   On branch main
   Your branch is up to date with 'origin/main'.

   nothing to commit, working tree clean
   ```
   
1. `git worktree list` <br />
   You should see something similar to the following.

   ```
   /home/edburns/workareas/myrepo  5bb3c2a [main]
   ```

1. Now, it's time to check out your `myfeature` branch using `git worktree`.

   ```
   git worktree add --track -b myfeature ../myrepo-01 origin/myfeature
   git worktree list
   ```
   
   You should see something similar to the following.
   
   ```
   /home/edburns/workareas/myrepo  5bb3c2a [main]
   /home/edburns/workareas/myrepo-01  49336e1 [myfeature]
   ```
   
   **IMPORTANT:** With `git worktree`, you only have one `.git` directory, in this case `myrepo/.git`.  If you do `ls -la` in `myrepo-01`, you will see a `.git` **file**.  It's name the same, but it is a file, not a directory.  So, even though you have two checked out branches you are not using twice as much disk space because the `myrepo-01` directory only has the most recent files of the checked out branch.
   
### Check out myotherfeature using git worktree add

1. `cd myrepo`
1. Now, it's time to check out your `myotherfeature` branch using `git worktree`.

   ```
   git worktree add --track -b myotherfeature ../myrepo-02 origin/myotherfeature
   git worktree list
   ```
   
   You should see something similar to the following.
   
   ```
   /home/edburns/workareas/myrepo  5bb3c2a [main]
   /home/edburns/workareas/myrepo-01  49336e1 [myfeature]
   /home/edburns/workareas/myrepo-02  52059ba [myotherfeature]
   ```

### Copy commits from myotherfeature to myfeature

Now comes the interesting part, how to to copy commits from `myotherfeature` to `myfeature`.

The following script, which I suggest you save as `~/bin/copyLastNWorktreeCommits.sh`, copies the last N commits from one worktree branch to another.  The script makes the simplifying assumption that the commits you want to copy are the most recent N commits.  If the commits you want to copy are not the most recent N commits, you can use `git rebase -i` to re-order the commits as described in this [decent tutorial from Atlassian](https://www.atlassian.com/git/tutorials/rewriting-history#git-rebase-i).

```bash
# pwd has the dest branch checked out
# first argument is relative path to source branch, checked out with worktree
# second argument is dest branch
# third argument is num commits
ontoValue=`git rev-parse HEAD`
sourceBranch=$1
destBranch=$2
numLastCommitsOnPrivateBranch=$3

pushd .
cd $1
startingCommit=`git rev-parse HEAD`
endingCommit=`git rev-parse HEAD~${numLastCommitsOnPrivateBranch}`

popd

git rebase --onto ${ontoValue} ${endingCommit} ${startingCommit}
git rebase HEAD ${destBranch}
```

Let's say we want to copy the last 3 commits from `myotherfeature` to `myfeature`.  Assuming `copyLastNWorktreeCommits.sh` is in your `~/bin` directory and is on your path, as described in the appendix, the following commands will accomplish this.

1. `cd myrepo-01`
1. `git status`

   You should see something similar to the following.
   
   ```
   On branch myfeature
   Your branch is up to date with 'origin/myfeature.

   nothing to commit, working tree clean
   ```
   
   Verify that you are in the correct directory. The script treats the current directory as the destination directory.
   
1. `copyLastNWorktreeCommits.sh ../myrepo-02 myfeature 3`

   You should see output similar to the following.

   ```bash
   First, rewinding head to replay your work on top of it...
   Applying: my change 01
   Applying: my change 02
   Applying: my change 03
   First, rewinding head to replay your work on top of it...
   Fast-forwarded main to HEAD.
   ```
   
1. Verify that the commits have been copied.

   ```
   git log -3
   ```
   
   You should see output similar to the following.
   
   ```
   commit 9f1116063f0ce1c097e3118fc096a764d678798e (HEAD -> main)
   Author: Ed Burns <email@address.com>
   Date:   Tue Jul 19 14:23:41 2022 -0400

       my change 03

   commit 072da86c6b8e360120aab1f2a6fd87368c64fc3d
   Author: Ed Burns <email@address.com>
   Date:   Tue Jul 19 14:23:27 2022 -0400

       my change 02

   commit 38036c3d4a4597b2ac070d32c236adc7b6106ae2
   Author: Ed Burns <email@address.com>
   Date:   Tue Jul 19 14:23:16 2022 -0400

       my change 01
   ```

1. Verify that the commits are ready to be pushed.

   ```
   git status
   On branch myfeature
   Your branch is ahead of 'origin/myfeature' by 3 commits.
     (use "git push" to publish your local commits)

   nothing to commit, working tree clean
   ```
   
   Don't be thrown off by the "nothing to commit".  Yes, it's true, you have nothing to commit to `myfeature`, because those three changes were already committed on `myotherfeature`.  So while you have nothing to commit, you **do** have something to push.
   
1. If you want, you can push those changes now, but doing so is an exercise for the reader.

### Summary

In this post we learned how to use `git worktree` to have three branches checked out at the same time, each in their own directories, conveniently located as siblings in the filesystem.  We learned how to copy commits from one branch to another in this arranchement.

### Appendix: bash basics preconditions

This appendix describes how to make it so you can call the script shown in the post from the command line.  As with everything in GNU/Linux, [there is more than one way to do it]({{ site.url }}/blog/2010/03/03/perils-8220there8217s-more-one-way-do-it8221.html).  I'm not even sure this is the best way. This is just how I do it.

1. Make sure you have a `bin` directory in your home directory.
1. Save the script to a file in that `bin` directory.  As stated in the post, I recommend naming the file `copyLastNWorktreeCommits.sh`.
1. Make sure the file has executable permissions. <br />
   `chmod ugo+x ~/bin/copyLastNWorktreeCommits.sh`.
1. Make sure your `bin` directory in your home directory is in your `$PATH` environment variable.
   - There are lots of ways to do this.  This one seems pretty reasonable: [https://linuxize.com/post/how-to-add-directory-to-path-in-linux/](https://linuxize.com/post/how-to-add-directory-to-path-in-linux/).
