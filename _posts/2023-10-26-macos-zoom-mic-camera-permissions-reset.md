---
layout: post
title:  macOS Catalina Zoom does not prompt for mic or camera permissions Solved
date:   2023-10-24 12:10-0400
comments: true
---

## Problem statement

Somewhere along the line, Zoom on macOS Catalina asked me for permission to use the mic or camera and I may have accidentally said no. How do I get it to prompt me again?

I tried the usual measures of `tccutil reset Microphone` but this returned a failure message. What's going on?

This blog post is my record of what I did to resolve the problem on macOS Catalina. Results are not guaranteed.

## Root cause

In my case, the root cause was the ownership of the `/Users/edburns/Library/Application Support/com.apple.TCC/TCC.db` file was incorrect. This file must be owned by me. Instead it was owned by some other user number. I remember making some bulk ownership changes somewhere along the line.

## Nutshell solution

1. Do all the stuff that allows you to modify `TCC.db`.
1. `chown` the `TCC.db` to me.
1. Restore all the stuff that prevents you from modifying `TCC.db`.
1. `tccutil reset Microphone`
1. Perform the action in Zoom that caused it to prompt for permission.
1. Say **Yes**.

### Detailed solution

#### Temporarily disable System Integrity Protection (SIP)

1. Shut down and reboot in "Recovery mode".
   1. Shut down.
   1. Hold down Command R while powering on.
   1. Release when the Apple logo appears.
1. When you get a menu bar, in the **Utilities menu** select **Terminal**.
1. The terminal should open as root.
1. `csrutil disable`.
1. Exit the terminal and quit the **Terminal** app.
1. Restart.

#### Ensure the TCC.db has the correct ownership

For an apparently very helpful resource on `TCC.db` see [A deep dive into macOS TCC.db](https://www.rainforestqa.com/blog/macos-tcc-db-deep-dive).

1. `cd ~Library/Application\ Support/com.apple.TCC`
   If you can't do this, grant "Full disk access" to your terminal program. Look elsewhere for how to do that.
1. `chown` the `TCC.db` file to your userid.
1. `cd` to get back to your home directory.

#### Re-enable System Integrity Protection (SIP)

1. Shut down and reboot in "Recovery mode".
   1. Shut down.
   1. Hold down Command R while powering on.
   1. Release when the Apple logo appears.
1. When you get a menu bar, in the **Utilities menu** select **Terminal**.
1. The terminal should open as root.
1. `csrutil enable`.
1. Exit the terminal and quit the **Terminal** app.
1. Restart.

#### Run the tccutil command

1. `tccutil reset Microphone`. If you want to reset other permissions, there is a big schema of names.  To reset everything replace `Microphone` with `All`. Case is sensitive.
1. You should see a message that indicates success.

