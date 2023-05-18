---
layout: post
title:  One minute video turns into fifteen minutes of WTF?
date:   2023-05-18 13:10-0400
comments: true
---

I strongly prefer Azure DevOps Boards for prodution quality project management, however, it's very hard to beat gratis. For this reason, the [Jakarta EE](https://jakarta.ee) project is using [GitHub Projects & Issues](https://github.com/features/issues). As the release co-coordinator for Jakarta EE 11, I need to gain mastery of GitHub Projects & Issues. So, I set out to understand this 71 second long video. How hard can it be? It's only one minute!

<iframe width="560" height="315" src="https://www.youtube.com/embed/o1wuW24Nv4E" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

I am glad this video exists. I understand the producers get to claim, "look it's so easy it fits in a one minute video." However, either because the video moves too fast or I move too slowly (I am over fifty years old), it took me fifteen minutes of play/pause/think/repeat to fully absorb the content in a way that I could replicate the steps shown. Is there a video that just runs the full ten minutes and explains it to me rather than this Gen-Z play/pause/think/repeat approach?

In the absence of such a video, here is my second-by-second breakdown of this action packed video.

- [00:04](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=4s) an issue exists with a [task   list](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists)   from some   [Investor-type](https://www.investopedia.com/terms/a/accreditedinvestor.asp)   persona. 

- [00:06](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=6s) using the [hover convert   technique](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists#about-issue-task-lists),   convert the three tasks to issues.   

- [00:07](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=7s) check the three checkboxes to close the newly created   issues. (Why? Seems a waste of time. But it's only one second, so I   guess it doesn't matter.)   

- [00:14](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=14s) Create a new project.

- [00:16](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=16s) Type the octothorpe. (You're not the only one who can be   obtuse, defunkt.) This drops down a   repository picker. There are probably some scope limitations   here. Perhaps you can only pick repositories in the same   Organization?

- [00:17](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=17s) Somehow more issues get added to a thing called **The Plan**,   which I don't recall being explicitly named. Also, the Investor   wanted these to be called **Epics** but I don't see that term   anywhere.

- [00:21](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=21s) And now we have a giant flat unordered mess of about 25 issues   with no hierarchical structure or organization. Also, there is a   **Status** field, where are the valid values for that field defined?   Is that automatic, like **The Plan**?

- [00:23](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=23s) Select (not click) the `+` icon to the right of the   **Assignees** column. This looks like some crude form of   categorization.  Apparently you must define your schema for   organizing your work on the fly. Apperently they invent the name   **Area**. The Investor wanted these to be called **Epics**. They   will not be happy with you using the wrong term.

  Anyhow, the video manually creates three **Area** values for the three   **Epics** listed by the Investor. Somehow icons are present. Where   do these come from?

- [00:27](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=27s) Select **Apply**. Somehow, magically, a new column   appears. How is the ordering determined? How are the members for   that column decided?

- [00:31](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=31s) Apparently the same process used for this **Area** column is   used to create a **Milestone** column. Again, there are icons, but   it's not clear how they are chosen.

- [00:33](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=31s) Each column apparently has a dropdown. However, there appears   to be a bug because the dropdown on the **Milestone** column has   **Area**. But because the video moves so fast I guess it doesn't   matter. Anyhow, they use the **Group by values** item. This causes   expand/collapse sections to appear for all the values of that   column, with the member rows that have that value grouped together.

- [00:41](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=41s) Now we select the search area at the top of the table. It   looks like they are demonstrating some kind of "filter"   feature. Yes. They are. They are showing how you can make it so the   table only shows issues belonging to the specified area.

- [00:46](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=46s) Now they use the drop down on the tab labeled **The Plan** and   select **Save changes to new view**. This creates a new tab. Somehow   a title is pre-filled as **Game loop Backlog**. Why does the word   **Backlog** not appear in the first tab? Is there some convention   over configuration thing happening here? The first tab is always   called **The Plan** and the next one **Backlog**?

- [00:50](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=50s) Now we have another tab **Standup**. Where did that come from?   I am guessing it came from using the same process to create the   **Game loop Backlog**? But again, how does it know the tab should be   called **Standup**? Anyhow, here we select the drop down on that tab   and select **Board**.

- [00:55](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=55s) you get columns for the **Status** field. Again, where is the   set of valid values for **Status** defined? How is it that the thing   knows that the columns should correspond to the valid values of   **Status**?

- [0:56](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=56s) - [01:11](https://www.youtube.com/watch?v=o1wuW24Nv4E&t=71s) We spend a luxurious fifteen seconds (20% of the total run time of the video) on a silly origami Octocat animation. 
