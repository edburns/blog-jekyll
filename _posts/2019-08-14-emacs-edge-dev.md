---
layout: post
title:  "Microsoft Edge Dev, Windows Subsystem for Linux, Emacs Markdown Mode, and URLs"
date:   2019-08-14 15:00 -0400
comments: true
---

This post shows how to make it so clicking on a hyperlink in GNU Emacs
running in the Windows Subsystem for Linux (WSL) on Windows 10 causes
the link to open up in Microsoft Edge Insider's Edition.

1. Install WSL

2. Install GNU Emacs

3. Install Markdown mode

4. [Install Microsoft Edge Insider](https://www.microsoftedgeinsider.com)
   
5. Make a shell script.  I put mine in `/home/edburns/bin/runedge.sh`

   ```
   /mnt/c/Program\ Files\ \(x86\)/Microsoft/Edge\ Dev/Application/msedge.exe $1
   ```

6. Put this in your `.emacs`:

<div class="lisp" style="font-family:monospace;"><ol><li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;"><span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">defun</span> browse-url-edge <span style="color: #66cc66;">&#40;</span>url <span style="color: #66cc66;">&amp;</span>optional new-window<span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span>shell-command</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; &nbsp; &nbsp;<span style="color: #66cc66;">&#40;</span>concat <span style="color: #ff0000;">&quot;/home/edburns/bin/runedge.sh &quot;</span> url<span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span><span style="color: #66cc66;">&#41;</span></div></li>
<li style="background: #f0f0f0;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp;</div></li>
<li style="background: #fcfcfc;"><div style="font: normal normal 1em/1.2em monospace; margin:0; padding:0; background:none; vertical-align:top;">&nbsp; &nbsp; <span style="color: #66cc66;">&#40;</span><span style="color: #b1b100;">setq</span> browse-url-browser-<span style="color: #b1b100;">function</span> 'browse-url-edge<span style="color: #66cc66;">&#41;</span></div></li>
</ol></div>
		

