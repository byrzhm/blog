---
title: Git Cheat Sheet
date: 2024-10-05 13:24:54 +0800
categories: [Toolkits, Git]
tags: [git cheat sheet]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## rev-list

- Print the list of commits reachable from the current branch.
```bash
git rev-list HEAD
```
- Print the list of commits on this branch, but not present in the upstream branch.
```bash
git rev-list @{upstream}..HEAD
```
- Format commits with their author and commit message (see also the porcelain [git-log[1]](https://git-scm.com/docs/git-log)).
```bash
git rev-list --format=medium HEAD
```
- Format commits along with their diffs (see also the porcelain [git-log[1]](https://git-scm.com/docs/git-log), which can do this in a single process).
```bash
git rev-list HEAD |
git diff-tree --stdin --format=medium -p
```
- Print the list of commits on the current branch that touched any file in the Documentation directory.
```bash
git rev-list HEAD -- Documentation/
```
- Print the list of commits authored by you in the past year, on any branch, tag, or other ref.
```bash
git rev-list --author=you@example.com --since=1.year.ago --all
```
- Print the list of objects reachable from the current branch (i.e., all commits and the blobs and trees they contain).
```bash
git rev-list --objects HEAD
```
- Compare the disk size of all reachable objects, versus those reachable from reflogs, versus the total packed size. This can tell you whether running `git repack -ad` might reduce the repository size (by dropping unreachable objects), and whether expiring reflogs might help.
```bash
# reachable objects
git rev-list --disk-usage --objects --all
# plus reflogs
git rev-list --disk-usage --objects --all --reflog
# total disk size used
du -c .git/objects/pack/*.pack .git/objects/??/*
# alternative to du: add up "size" and "size-pack" fields
git count-objects -v
```
- Report the disk size of each branch, not including objects used by the current branch. This can find outliers that are contributing to a bloated repository size (e.g., because somebody accidentally committed large build artifacts).
```bash
git for-each-ref --format='%(refname)' |
while read branch
do
	size=$(git rev-list --disk-usage --objects HEAD..$branch)
	echo "$size $branch"
done |
sort -n
```
- Compare the on-disk size of branches in one group of refs, excluding another. If you co-mingle objects from multiple remotes in a single repository, this can show which remotes are contributing to the repository size (taking the size of `origin` as a baseline).
```bash
git rev-list --disk-usage --objects --remotes=$suspect --not --remotes=origin
```

## submodule

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

- [https://education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)
- [https://git-scm.com/docs/git-rev-list](https://git-scm.com/docs/git-rev-list)
- [https://iphysresearch.github.io/blog/post/programing/git/git_submodule/](https://iphysresearch.github.io/blog/post/programing/git/git_submodule/)
- [https://git-scm.com/book/en/v2/Git-Tools-Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)


