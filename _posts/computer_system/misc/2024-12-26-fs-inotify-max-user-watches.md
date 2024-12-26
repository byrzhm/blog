---
title: fs.inotify.max_user_watches
date: 2024-12-26 15:56:00 +0800
categories: [Computer System, Misc]
tags: [os, inotify]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## TL;DR

Use the following command to change the `fs.inotify.max_user_watches`.
```shell
sudo sysctl -n -w fs.inotify.max_user_watches=722104
```

## Relavent Resources

- inotify
    - [ArchLinux Wiki](https://man.archlinux.org/man/inotify.7.en)
    - [man page](https://man7.org/linux/man-pages/man7/inotify.7.html)