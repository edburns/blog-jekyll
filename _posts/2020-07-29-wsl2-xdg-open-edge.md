---
layout: post
title:  "WSL2 az login xdg-open and Microsoft Edge (or any other browser)"
date:   2020-07-29 15:10-0400
comments: true
---

If you ever run into a problem with `az login` opening a Windows browser
to complete the login, this blog post may help.  I ran into this today.
When I did `az logout` followed by `az login` I saw the following, and
the login did not complete succesfully.

```
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: firefox: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: iceweasel: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: seamonkey: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: mozilla: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: epiphany: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: konqueror: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: chromium: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: chromium-browser: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: google-chrome: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: www-browser: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: links2: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: elinks: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: links: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: lynx: not found
/usr/bin/xdg-open: 851: /usr/bin/xdg-open: w3m: not found
xdg-open: no method available for opening 'https://login.microsoftonline.com/common/oauth2/authorize?response_type=code&client_id=04b07795-8ddb-461a-bbee-02f9e1bf7b46&redirect_uri=http://localhost:8400&state=jq29agg65cx8xmh3axhv&resource=https://management.core.windows.net/&prompt=select_account'
```

I thought:

```
¯\_(ツ)_/¯
```

After poking around a bit, I arrived at the following solution.

### 1. Make an executable script in my bin directory that runs the browser on the windows side

See step 5 in [my previous post](/blog{% post_url 2019-08-14-emacs-edge-dev %}).
Modify the script to reference your preferred browser.

### 2. Create an edge.desktop file in the correct place

Make it so this file exists: `~/.local/share/applications/edge.desktop`
and has the following content.

```
[Desktop Entry]
Encoding=UTF-8
Version=1.0
Type=Application
NoDisplay=true
Exec=/home/edburns/bin/runedge.sh %u
Name=Edge
Comment=Custom definition for Edge Beta
```

You'll need to modify the value of the `Exec` line accordingly.  Also,
if you are using a different browser, you'll need to change the name of
the file to `whaterYourBrowser.desktop`.  Close the file when done editing.

### 3. Use `xdg-settings` to set the `default-web-browser`

Now that you have created `edge.desktop` in the correct place, you can
tell `xdg-settings` that it is the default browser.

```
xdg-settings set default-web-browser edge.desktop
```

The first time I ran this, it took a while, and I observed it added this
line to the `edge.desktop` file.

```
MimeType=x-scheme-handler/unknown;x-scheme-handler/about;x-scheme-handler/https;x-scheme-handler/http;text/html;
```

You can verify the setting with the following command.

```
xdg-settings get default-web-browser
```

### 4. `az login` should pop up the browser.

```
az logout
az login
```
