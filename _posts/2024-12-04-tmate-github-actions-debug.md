---
layout: post
title:  "Using the tmate GitHub Action from Max Schmitt to aid debugging GitHub Actions workflows"
date:   2024-12-04 13:30-0500
comments: true
---

### Executive Summary

This post shows how to use the [action-tmate](https://github.com/mxschmitt/action-tmate) GitHub Action to allow you to SSH into the runner for your GitHub Actions workflow.

### Details

#### Prerequisites

* You have an existing GitHub Actions workflow that runs, at least somewhat.

* You have set up your SSH key with GitHub and can use it to push commits to the repo which has the GitHub Actions workflow.

#### Steps

1. Put these lines in your GitHub Actions workflow. Personally I always have a `preflight` job that sets up some very basic things. I put the below lines as the first thing in the first job **after** `preflight`.

  ```
      after-preflight:
          needs: preflight
          runs-on: ubuntu-latest
          steps:
              - name: Setup tmate session
                uses: mxschmitt/action-tmate@v3
                timeout-minutes: 120
                with:
                  detached: true
  ```

1. When the workflow reaches the `after-preflight` job, you'll see a bunch of tmate related stuff in the job log, something like:

   ```
   Run mxschmitt/action-tmate@v3
   Get:1 file:/etc/apt/apt-mirrors.txt Mirrorlist [142 B]
   Hit:2 http://azure.archive.ubuntu.com/ubuntu jammy InRelease
   Hit:6 https://packages.microsoft.com/repos/azure-cli jammy InRelease
   Get:3 http://azure.archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
   Get:7 https://packages.microsoft.com/ubuntu/22.04/prod jammy InRelease [3632 B]
   Get:4 http://azure.archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
   Get:5 http://azure.archive.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
   Get:8 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main armhf Packages [15.7 kB]
   Get:9 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main arm64 Packages [40.2 kB]
   Get:10 https://packages.microsoft.com/ubuntu/22.04/prod jammy/main amd64 Packages [172 kB]
   Get:11 http://azure.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [2180 kB]
   Get:12 http://azure.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1177 kB]
   Get:13 http://azure.archive.ubuntu.com/ubuntu jammy-updates/multiverse Translation-en [11.5 kB]
   Get:14 http://azure.archive.ubuntu.com/ubuntu jammy-security/main amd64 Packages [1956 kB]
   Get:15 http://azure.archive.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [957 kB]
   Fetched 6897 kB in 1s (6399 kB/s)
   Reading package lists...
   Reading package lists...
   Building dependency tree...
   Reading state information...
   xz-utils is already the newest version (5.2.5-2ubuntu1).
   openssh-client is already the newest version (1:8.9p1-3ubuntu0.10).
   0 upgraded, 0 newly installed, 0 to remove and 38 not upgraded.
   ssh redacted@nyc1.tmate.io

   Notice: SSH: ssh redacted@nyc1.tmate.io
   Notice: or: ssh -i <path-to-private-SSH-key> redacted@nyc1.tmate.io
   ```
   
1. Here's where the magic happens. Max Schmitt's tmate action somehow uses the SSH key you configured as the SSH key for the connection. So when you run either of those two commands, the ssh "just works" and you are able to get into the runner. I find this is useful to be able to poke around and debug things.

Thanks of course to [Max Schmitt](https://github.com/mxschmitt) for creating and maintaining the software and to my colleagues [Jianguo](https://github.com/majguo) and [Zhihao](https://github.com/backwind1233/) for reducing my [2nd order ignorance](https://aka.ms/rockstarignorance/) about it.
