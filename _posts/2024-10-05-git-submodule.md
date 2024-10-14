---
title: Git Submodule
date: 2024-10-05 13:24:54 +0800
categories: [Git, Submodule]
tags: [git]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## How `git submodule` works?

## `git submodule sync`

## `git submodule update`

## Common Usage

### `https` to `ssh`

1. Edit the `.gitmodules` file
```ini
[submodule "path/to/submodule"]
    url = https://github.com/user/repo.git
```
Change it to:
```ini
[submodule "path/to/submodule"]
    url = git@github.com:user/repo.git
```
2. Update the submodule configuration
```bash
git submodule sync
```
3. `git config --edit` to see the effects
4. Pull the latest changes
```bash
git submodule update --init --recursive
```


## References

* [https://iphysresearch.github.io/blog/post/programing/git/git_submodule/](https://iphysresearch.github.io/blog/post/programing/git/git_submodule/)
* [https://git-scm.com/book/en/v2/Git-Tools-Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
