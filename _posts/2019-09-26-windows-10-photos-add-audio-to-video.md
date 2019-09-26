---
layout: post
title:  "How to add audio to a video in 'Photos for Windows 10'"
date:   2019-09-26 17:00 -0400
comments: true
---

## How to add audio to a video in "Photos for Windows 10".

1. Start Photos

2. Click Video Projects

3. Click "Custom audio" in the top right of the window, Between
   "Background music" and "Sync to OneDrive".-
   
4. Set the "in" marker in the timeline where you want the audio to start.

5. You may need to work with the audio before you add it.  I used these
   steps.
   
   a. The audio file in my case was a narration I recorded using Windows
      10 Voice Recorder.  This gives you files in `.m4a` format.
      
   b. I wanted to trim the file so I had to convert it to WAV so I could
      edit it with Audacity.
      
   c. I installed ffmpeg for windows from
      [https://lame.buanzo.org/#lamewindl](https://lame.buanzo.org/#lamewindl).
      This gives you `ffmpeg.exe` which you can use from the command
      prompt.
      
   d. CD to the directory containing the `.m4a` file.
   
   e. Convert it like this: `C:\PROGRA~2\FFMPEG~1\ffmpeg.exe -i input.m4a output.wav`

6. Click "Add audio file", select the `output.wav` file in the file
   browser, click ok, click Done.
   
7. Click "Finish video" and select a place to save the file.


