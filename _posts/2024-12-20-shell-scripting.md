---
title: Shell Scripting
date: 2024-12-20 13:25:00 +0800
categories: [Scripting, shell]
tags: [shell]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## Frequently Used Command Line Tools

### `tr`
...

### `sort`
...

### `uniq`
...

### `awk`
...

## Real World Problems

### Word Frequency Count

```bash
# Read from the file words.txt and output the word frequency list to stdout.
tr -s '[:space:]' '\n' < words.txt | sort | uniq -c | sort -nr | awk '{print $2, $1}'
```

## References

- [word-frequency](https://leetcode.cn/problems/word-frequency/description/)
