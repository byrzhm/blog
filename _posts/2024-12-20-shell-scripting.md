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

### `basename`
...

## Real World Problems

### Word Frequency Count

```bash
# Read from the file words.txt and output the word frequency list to stdout.
tr -s '[:space:]' '\n' < words.txt | sort | uniq -c | sort -nr | awk '{print $2, $1}'
```

### Add Prefix to All the Files in a Directory

Run the following command:
```bash
chmod +x add_prefix.sh
./add_prefix.sh /path/to/directory prefix_
```
And here is the `add_prefix.sh`:
```bash
#!/bin/bash

# Check if the directory and prefix are provided as arguments
if [ $# -ne 2 ]; then
    echo "Usage: $0 <directory> <prefix>"
    exit 1
fi

# Assign arguments to variables
directory=$1
prefix=$2

# Check if the directory exists
if [ ! -d "$directory" ]; then
    echo "Error: Directory $directory does not exist."
    exit 1
fi

# Iterate over all files in the directory
for file in "$directory"/*; do
    # Skip directories
    if [ -f "$file" ]; then
        # Extract the base name of the file
        basename=$(basename "$file")
        # Rename the file with the prefix
        mv "$file" "$directory/$prefix$basename"
    fi
done

echo "Prefix added to all files in $directory."
```
{: file='add_prefix.sh' }

## References

- [word-frequency](https://leetcode.cn/problems/word-frequency/description/)
