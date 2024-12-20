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

### Change Prefix from One to Another

```bash
#!/bin/bash

# Check if the directory, old prefix, and new prefix are provided as arguments
if [ $# -ne 3 ]; then
    echo "Usage: $0 <directory> <old_prefix> <new_prefix>"
    exit 1
fi

# Assign arguments to variables
directory=$1
old_prefix=$2
new_prefix=$3

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

        # Check if the file name starts with the old prefix
        if [[ "$basename" == "$old_prefix"* ]]; then
            # Remove the old prefix and add the new prefix
            new_name="$new_prefix${basename#$old_prefix}"
            # Rename the file
            mv "$file" "$directory/$new_name"
        fi
    fi
done

echo "Prefix changed from '$old_prefix' to '$new_prefix' in $directory."
```
{: file='change_prefix.sh' }

## References

- [word-frequency](https://leetcode.cn/problems/word-frequency/description/)
