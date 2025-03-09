---
title: Introduction to Redis
date: 2025-02-19 18:43:00 +0800
categories: [Software, Redis]
tags: [redis]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

## What is Redis?
Redis (Remote Dictionary Server) is an open-source, in-memory key-value data store that is used as a database, cache, and message broker. It is known for its high performance, scalability, and support for various data structures such as strings, hashes, lists, sets, and sorted sets.

## Why Use Redis?
- **High Performance**: Since Redis operates in memory, it provides low-latency data access compared to traditional disk-based databases.
- **Rich Data Structures**: Supports a variety of data structures like strings, lists, sets, hashes, and more.
- **Persistence**: Allows data persistence through RDB (snapshotting) and AOF (Append Only File) mechanisms.
- **Replication**: Supports master-slave replication for high availability.
- **Pub/Sub Messaging**: Can be used as a message broker with the Publish/Subscribe model.
- **Atomic Operations**: Commands are atomic, making transactions safe and reliable.

## Installing Redis

### On Ubuntu/Debian
```bash
sudo apt update
sudo apt install redis
```

After installation, start Redis:
```bash
sudo systemctl start redis
```

Enable Redis to start on boot:
```bash
sudo systemctl enable redis
```

### On macOS (via Homebrew)
```bash
brew install redis
brew services start redis
```

## On Windows
Use the official Redis binaries for Windows or install via WSL (Windows Subsystem for Linux).

## Verifying Redis Installation
To check if Redis is running, use:
```bash
redis-cli ping
```

If Redis is running, it should return:
```
PONG
```

## Basic Redis Commands
### Storing and Retrieving Data
```bash
redis-cli set key1 "Hello, Redis"
redis-cli get key1
```

Expected output:
```
"Hello, Redis"
```

### Deleting a Key
```bash
redis-cli del key1
```

### Checking if a Key Exists
```bash
redis-cli exists key1
```

Returns `1` if the key exists, `0` otherwise.

### Working with Lists
```bash
redis-cli rpush mylist "item1"
redis-cli rpush mylist "item2"
redis-cli lrange mylist 0 -1
```

Output:
```
1) "item1"
2) "item2"
```

### Using Sets
```bash
redis-cli sadd myset "a"
redis-cli sadd myset "b"
redis-cli smembers myset
```

Output:
```
1) "a"
2) "b"
```

### Using Hashes

```bash
redis-cli hset user:1 name "Alice" age 25
redis-cli hgetall user:1
```

Output:
```
1) "name"
2) "Alice"
3) "age"
4) "25"
```

## Persistence in Redis

Redis provides two main persistence options:
- RDB (Redis Database Backup): Creates snapshots of the dataset at intervals.
- AOF (Append Only File): Logs all write operations for better durability.

Modify `/etc/redis/redis.conf` to configure persistence options.

## Redis as a Cache
To use Redis as a cache, enable **Eviction Policies**:
```bash
redis-cli config set maxmemory 100mb
redis-cli config set maxmemory-policy allkeys-lru
```

This sets a maximum memory limit of 100MB and enables the **Least Recently Used (LRU)** eviction policy.

## Setting Up Redis Security

By default, Redis is accessible only locally. To enhance security:
- **Set a password** in `redis.conf`:
    ```
    requirepass mysecurepassword
    ```
- **Bind Redis to specific IPs**:
    ```
    bind 127.0.0.1
    ```
- **Use firewalls and access control lists (ACLs)**.

## Conclusion

Redis is a powerful, high-performance data store with diverse use cases, from caching to real-time analytics and messaging. This tutorial introduced its installation, basic commands, and key features. Next, explore advanced topics like Lua scripting, clustering, and high availability setups.
