---
title: Daemon proxy configuration
date: 2025-02-17 17:51:00 +0800
categories: [Software, Docker]
tags: [docker]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

- [Daemon proxy configuration](https://docs.docker.com/engine/daemon/proxy/)

---

If your organization uses a proxy server to connect to the internet, you may need to
configure the Docker daemon to use the proxy server. The daemon uses a proxy server to
access images stored on Docker Hub and other registries, and to reach other nodes in a Docker swarm.

This page describes how to configure a proxy for the Docker daemon. For instructions on
configuring proxy settings for the Docker CLI, see [Configure Docker CLI to use a proxy server](https://docs.docker.com/engine/cli/proxy/).

> Proxy configurations specified in the `daemon.json` are ignored by Docker Desktop.
> If you use Docker Desktop, you can configure proxies using the [Docker Desktop settings](https://docs.docker.com/desktop/settings-and-maintenance/settings/#proxies).
{: .prompt-info }


There are two ways you can configure these settings:
- [Configuring the daemon](#daemon-configuration) through a configuration file or CLI flags
- Setting [environment variables](#environment-variables) on the system

Configuring the daemon directly takes precedence over environment variables.

## Daemon configuration

You may configure proxy behavior for the daemon in the `daemon.json` file,
or using CLI flags for the `--http-proxy` or `--https-proxy` flags for the `dockerd` command.
Configuration using `daemon.json` is recommended.

```json
{
    "proxies": {
        "http-proxy": "http://proxy.example.com:3128",
        "https-proxy": "https://proxy.example.com:3129", // or "http://..."
        "no-proxy": "*.test.example.com,.example.org,127.0.0.0/8"
    }
}
```

After changing the configuration file, restart the daemon for the proxy configuration to take effect:

```
$ sudo systemctl restart docker
```

## [Environment variables](https://docs.docker.com/engine/daemon/proxy/#environment-variables)
