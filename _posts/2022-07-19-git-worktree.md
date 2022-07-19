---
layout: post
title:  "Using git worktree and a script to copy commits from one branch to another in the same repo"
date:   2022-07-19 09:25-0400
comments: true
---

## Appendix: bash basics preconditions

This appendix describes how to make it so the script shown in the post can be made so you can call it from the command line.  As with everything in GNU/Linux, [there is more than one way to do it]({{ site.url }}/blog/2010/03/03/perils-8220there8217s-more-one-way-do-it8221.html).  I'm not even sure this is the best way. This is just how I do it.

1. Make sure you have a `bin` directory in your home directory.
1. Save the script to a file in that `bin` directory.  As stated in the post, I recommend naming the file `copyLastNWorktreeCommits.sh`.
1. Make sure the file has executable permissions.  `chmod ugo
