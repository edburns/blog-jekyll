---
layout: post
title:  "How to use Microsoft OneDrive when your mac HD is Case Sensitive"
date:   2019-10-16 10:15 -0400
comments: true
---

## How to use Microsoft OneDrive when your mac HD is Case Sensitive

As a Unix user since 1990 I know that file systems should be case
sensitive.  So whenever I get a new Mac, I make sure to set it up with
a case sensitive filesystem.  After doing this with my Microsoft
supplied Mac, I was a bit surprised to learn that the Microsoft
OneDrive macOS client does not allow you to place your OneDrive folder
on a case sensitive volume, nor on an external volume.

No worries!

I was able to create an APFS case insensitive volume internal and put
the OneDrive folder there.

### Part 1: Create the Volume

1. Start Disk Utility

2. In the left navigation pane, context click (right click) `Macintosh
   HD - Data`

3. Choose "Add APFS Volume..."

4. For Name choose `OneDrive`

5. For Format choose APFS or APFS (Encrypted).  I chose Encrypted for
   added security.

   ![APFS volume]({{ site.url }}/blog/assets/apfs-volume.png "APFS volume")

6. Click "Size Options...".  Choose an appropriate size.  I chose 2GB
   for Reserve and Quota Size.  Click OK.

7. Click "Add".

8. Click "Done".

### Part 2: Connect OneDrive

1. When you start OneDrive on the Mac, it should prompt you to "Set up
   One Drive".  Go through the login procedures, which may include two
   factor authentication.

2. When you get to the "This Is Your OneDrive Folder" page, click
   "Change Location".

3. In the file browser that pops up, choose "OneDrive" then click
   "Choose this locatin", as shown here.

   ![OneDrive Choose Location]({{ site.url }}/blog/assets/onedrive-choose-location.png "OneDrive Choose Location")

4. Click "Next" then click "Next".

5. Then click "Open my OneDrive Folder".
