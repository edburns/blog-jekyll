---
layout: post
title:  "Git Tags and Semver"
date:   2018-11-13 12:41 +0100
comments: true
---

## Executive Summary

Debugging a Swift `Package.swift` problem that turned out to be
incorrectly git tags that did not sort correctly with respect to semver.

## Details

I was trying to run the steps in an internal README to create a "Hello
World" for a simple Swift project and could not understand why some code
that had been removed from an internal dependency was still showing up
in the "Hello World".  This code had been removed from the most recent
release of the internal dependency.  Upon investigation it turned out
that the git tags for the internal dependency, when sorted using
[semver](https://semver.org/), showed an old release as the most
recent.

I found it useful to create an alias that outputs the git tags sorted by
semver.  I used the handy `semver` npm module:

```
sudo npm install --verbose --global semver
```

and with that the following bash function works:

```
function semvergittags() {
  semver `git tag -l | tr '\n' ' '`
}
```

For example:

```
ejburns$ semvergittags
0.1.11
1.0.0-ea07-b0
1.0.0-ea07-b1
1.0.0-ea08-b1
1.0.0-ea11-b1
1.0.0-ea12-b1
1.0.0-ea13
1.0.0-ea14
1.0.0-ea15
1.0.0-ea16
1.0.0-ea17
1.0.0-ea18
1.0.0-ea19
1.0.0-ea20
1.0.0-ea21
1.0.0-ea22
1.0.0-ea23
1.0.0-ea24
1.0.0-ea25
```

I fixed the git tags using this
[answer from stack overflow](https://stackoverflow.com/questions/1028649/how-do-you-rename-a-git-tag):


```
git tag new old
git tag -d old
git push origin :refs/tags/old
git push --tags
```

I gave the answer an upvote because they shared this additional tip:

> Finally, make sure that the other users remove the deleted tag. Please
> tell them (co-workers) to run the following command:

> `git pull --prune --tags`

That's the sort of thing that could easily be forgotten and cause more
head scratching.
