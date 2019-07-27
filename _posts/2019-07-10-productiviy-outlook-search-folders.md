---
layout: post
title:  "Productivity: Use Outlook Search Folders"
date:   2019-07-10 12:00 -0400
comments: true
---

![One Weird Trick]({{ site.url }}/blog/assets/20190710-outlook-one-weird-trick.PNG "One Weird Trick")

I've been an emacs user since 1991, and from 1993 until 2019-06-10 I
used [Emacs VM mode](http://www.nongnu.org/viewmail/) to read email.  I
still use it for my personal email, but I've given up using it for
Microsoft mail and instead started using Outlook.  The most essential
feature I need is what VM mode called [Virtual
Folders](https://www.emacswiki.org/emacs/ViewMailVirtualFolders):
`"Virtual folders contain messages from one or more real folders (see
ViewMailFolders) with various filters applied to select which messages
to include."` My favorite aspect of this feature was that you could use
regular expressions to specify the filters.

I used the feature to craft a 7 level, prioritized new mail reading
process in line with "First things first" techniques espoused by [Steven
Covey](http://eisenhower-matrix.com/first-things-first/).  When I would
do my main email session of the day, I'd go through the 7 virtual
folders to make sure I acted on the important emails first.

Outlook has a similar feature, called "Search Folders".  The only
shortcoming appears to be that you cannot use regular expressions.  That
and the fact that Emacs VM had the feature in 1993, while Outlook for
Windows 3.1 was only in its very early version at that point.

This blog post shows how to approximate the VM Virtual Folders feature
with Outlook Search Folders.

## The Emacs VM side.

The ["Search
Folders"](https://support.office.com/en-us/article/Use-Search-Folders-to-find-messages-or-other-Outlook-items-c1807038-01e4-475e-8869-0ccab0a56dc5)
help page shows how to do it, but my secret sauce is my ordered list of
virtual folders, prevented in abbreviated form here.  I have used the
"Search Folders" feature to approximate what I had with VM.  After this
bunch of lisp, I'll show how I created search folders for some of these
virtual folders.

<div class="lisp" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">setq</span> vm-virtual-folder-alist</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;'<span style="color: #66cc66;">&#40;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;1-urgent&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[uU][rR][gG][eE][nN][tT].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[tT][iI][mM][eE].[sS][eE][nN][sS][iI][tT][iI][vV][eE].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[vV][oO][iI][cC][eE][mM][aA][iI][lL].*[fF][rR][oO][mM].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[aA][cC][tT][iI][oO][nN].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[pP][lL][eE][aA][sS][eE].*[rR][eE][pP][lL][yY]&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[pP][lL][eE][aA][sS][eE].*[rR][eE][sS][pP][oO][nN][dD].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[pP][lL][eE][aA][sS][eE].*[rR][eE][aA][dD].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[mM][aA][rR][kK].*[cC][aA][lL][eE][nN][dD][aA][rR].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;2-important-people-a&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/issuetracker&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[pP][rR][oO][dD][uU][cC][tT] [LL][iI][fF][eE][cC][yY][cC][lL][eE] [SS][uU][iI][tT][eE].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;Larry Ellison&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;2-important-people-b&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/issuetracker&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;Manfred Riem&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[bB][eE][aA][nN].*[vV][aA][lL][iI][dD][aA][tT].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[oO][iI][mM].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>header <span style="color: #ff0000;">&quot;X-folder: issuetracker&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;3-ejburns&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;ed.burns@sun.com&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;edburns@java.net&quot;</span><span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;4-dev-lists-primary&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/dev-lists&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/jsr369-experts&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/jsr372-experts&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/jsr362-experts&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr372-experts@javaserverfaces-spec-public.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr369-experts@servlet-spec.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*dev@javaserverfaces.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*dev@facelets.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*edburns@java.net.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*nb68-jsf2-dev.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*glassfish-guerilla.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;5-dev-lists-secondary&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/dev-lists&quot;</span> <span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/public_cloud_info_ww_grp&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-303-eg.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*dev@glassfish.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-ri-integration-list.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*ee6jboss-int.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*public_cloud_info_ww_grp.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*eearch@sun.com.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*[jJ][aA][vV][aA][eE][eE]_[aA][rR][cC][hH][iI][tT][eE][cC][tT][uU][rR][eE]_[wW][wW].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*el-next.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*webtier-tech@sun.com.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;6-user-lists&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/user-lists&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*webtier@glassfish.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*users@glassfish.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[jJ][sS][fF].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*users@glassfish.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[fF][aA][cC][eE][sS].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;7-lastMonth&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>older-than 30<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>deleted<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;8-lastWeek&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>older-than 7<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>deleted<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;9-yesterday&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>older-than 1<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span> <span style="color: #66cc66;">&#40;</span>deleted<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;$-jsr-314-open&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/dev-lists&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-314-comments.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-314-open.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-314-eg.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;non-ejburns-issues&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/.mail/issuetracker&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">not</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>text <span style="color: #ff0000;">&quot;.*Assignee: ejburns.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>text <span style="color: #ff0000;">&quot;.*Assignee: ejburns.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;lastYear&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">and</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>sent-after <span style="color: #ff0000;">&quot;31 Dec 2016&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>sent-before <span style="color: #ff0000;">&quot;01 Jan 2018&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;myfaces&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*myfaces.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;webtier-alignment&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*webtier-alignment.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;jsr-252-comments&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*jsr-252-comments.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;jsr-154-eg&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*servlet-spec-eg.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;jsr-303-eg&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;[jJ][sS][rR]-303-[eE][gG]@[jJ][cC][pP].[oO][rR][gG]&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;out-of-office&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[oO][uU][tT].*[oO][fF].*[oO][fF][fF][iI][cC][eE].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[wW][fF][hH].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;hudson&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[hH][uU][dD][sS][oO][nN].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[jJ][eE][nN][kK][iI][nN][sS].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[hH][uU][dD][sS][oO][nN].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[jJ][eE][nN][kK][iI][nN][sS].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[oO][dD][xX][ ][gG][iI][tT][lL][aA][bB].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;bugdb&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[bB][uU][gG][dD][bB].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;github&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[nN][oO][tT][iI][fF][iI][cC][aA][tT][iI][oO][nN][sS]@[gG][iI][tT][hH][uU][bB].[cC][oO][mM].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;confluence&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[cC][oO][nN][fF][lL][uU][eE][nN][cC][eE].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;git&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[eE][sS][eE] [sS][uU][pP][pP][oO][rR][tT].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;commits&quot;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*commits@.*java.net.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>recipient <span style="color: #ff0000;">&quot;.*cvs@.*java.net.*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;jira&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[jJ][iI][rR][aA].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;ODCS:.*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[wW][eE][bB][lL][oO][gG][iI][cC].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;calendar&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[mM][eE][eE][tT][iI][nN][gG] [iI][nN][vV][iI][tT][eE].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[MM][eE][eE][tT][iI][nN][gG] [WW][iI][tT][hH][dD][rR][aA][wW][nN].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>subject <span style="color: #ff0000;">&quot;.*[MM][eE][eE][tT][iI][nN][gG] [UU][pP][dD][aA][tT][eE][dD].*&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;jenkins&quot;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #66cc66;">&#40;</span><span style="color: #ff0000;">&quot;/home/ejburns/fmail/INBOX&quot;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">or</span> <span style="color: #66cc66;">&#40;</span>author <span style="color: #ff0000;">&quot;.*[jJ][eE][nN][kK][iI][nN][sS].*&quot;</span><span style="color: #66cc66;">&#41;</span> </div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp;<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
</ol></div>

I only use the "numbered" virtual folders in my daily email scan.  But
if you have an enormous volume of incoming mail, as I did when I was the
spec lead of several JSRs at the same time, the other ones were very
helpful.  I will say that since the advent of tools like Slack and
Microsoft Teams, the volume of email is less.

## The Outlook Side

### Create your First Search Folder: 1-urgent

Click the "Folder" menu, then the "New Search Folder" button, as shown next.

![New Search Folder]({{ site.url }}/blog/assets/20190710-outlook-new-search-folder.PNG "New Search Folder")

In the "New Search Folder" dialog that appears, scroll the content of
the "Select a Search Folder" pane down to the bottom and select "Create
a custom Search Folder".  Click "Choose...".

In the "Name" field, type `1-urgent`.

Click the "Browse..." button just beneath the "Mail from these folders
will be included in this Search Folder: heading.  Uncheck the
ridiculously tiny little checkbox next to your account name and check
the ridiculously tiny little checkbox next to "Inbox".  Make sure
"Search subfolders" is checked, as shown next.

![Select Folder(s)]({{ site.url }}/blog/assets/20190710-outlook-select-folders.PNG "Select Folder(s)")

This is important, otherwise you'll see "Deleted", "Sent", "Drafts",
"Junk" and other undesirable nonsense in your Search Folders.  Click "OK".

Click "Criteria".  Selct the "More Choices" Tab.  Check the checkbox next
to "Only items that are:" and change the value to "unread".  If you
neglect this step you'll see things you've already seen, which defeats
the purpose of the exercise.

Select the "Advanced" tab.  Drop down the "Field"
menu, mouse over "Frequently-used fields", and mouse over to "Subject".
Click on "Subject".  Leave the "Condition" as `contains`.  In the value
enter "urgent".  Click "Add to List".

Do this again and again for lines 6 - 13 in the lisp above.  The matches
are case-insensitive by default, and this thing doesn't appear to accept
regexp anyway, so that's why you don't need the goofy looking
`.*[uU][rR][gG][eE][nN][tT].*` type syntax.  However, as you'll see,
this is a lot more clicking than just writing some simple regexps.  I'd
rather the regexps.  

After adding all the subject variants click "OK", then click "OK"
again.  This will cause the "1-urgent" Search folder to appear in the
"Search Folders" section on the left navigation, as shown next.

![Left Nav]({{ site.url }}/blog/assets/20190710-outlook-left-nav.PNG "Left Nav")

I like to add the Search Folders to my favorites.  To do this, right
click on the Search Folder and choose "Add to Favorites".

&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    
&nbsp;    

### Create Your Second Search Folder: 2-important-people-a

Right click the "Search Folders" heading in the left nav bar and choose
"New Search Folder".  Type the name "2-important-people-a" and do the
"Browse..." ridiculously tiny little checkbox trick again.

Click "Criteria".  Select the "Advanced" tab.  Drop down the "Field"
menu, mouse over "Address fields", and mouse over to "From".  Click on
"From".  Leave the "Condition" as `contains`.  In the value enter the
name of the important person.  Click "Add to List".

After adding all the important people, click "OK", then click "OK"
again.  This will cause the "2-important-people-a" Search folder to
appear in the "Search Folders" section on the left navigation.  Add this
to the favorites as well.

### Create Your Third Search Folder: 2-important-people-b

This is just the same as the preceding step, but for people that are not
as important to you as `important-people-a`.  I find having two levels
of important people is enough for me, but YMMV.  I tend to put bosses
and their bosses in the "a" category and my important collaborators in
the "b" category.  Everyone else falls into the timed categories I'll
show later.

### Create Your Fourth Search Folder: 3-&lt;your name&gt;

This folder is for messages sent directly to you, not messages that you
receive by virtue of being on a mailing list.

Create this using the steps as for the preceding Search Folders, but
when you get to the "Criteria" step, it's different.  In the "Messages"
tab, In the text field next to "Sent To...", fill in a semicolon
separated list of the identifiers for your email address.  In my case, I
put `Ed Burns <Edward.Burns@microsoft.com>; Edward Burns;
edburns@microsoft.com; edward.burns@microsoft.com` because these are all
I can be reached.

### Create the "date based" Search Folders: 7-lastMonth, 8-lastWeek, 9-yesterday

Create these folders in a similar fashion as above, but when you get to
criteria selection, Drop down the "Field" menu, mouse over "Date/Time
fields", and mouse over to "Sent".  Click on "Sent".  Change the value
of "Condition" to "Last Month".  Click "Add to List".  Click "OK" and "OK".

Create Search Folders for "Last Week" and "Yesterday" in a similar
manner.

## Summary

One of the [rockstar programmer](https://ridingthecrest.com/) skills I
found is "mastery of tools".  All of us, not just programmers, need to
have mastery of email.  I hope this one weird trick helps you save time
on email.



