---
title: Docker Network Error
date: 2025-02-16 21:16:00 +0800
categories: [Software, Docker]
tags: [docker]     # TAG names should always be lowercase
pin: false
math: false
mermaid: false
---

```
> docker run hello-world
Unable to find image 'hello-world:latest' locally
docker: Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers).
See 'docker run --help'.
```

The error suggests a network issue while trying to pull the `hello-world` image from Docker Hub. Here's a step-by-step approach to troubleshooting and solving it:

1. **Check your internet connection**:
   - Ensure that your computer has internet access and can reach external websites (like Docker Hub).
   - Try pinging `docker.io` or any other website to verify the connection.

2. **Check Docker daemon status**:
   - Sometimes the Docker daemon itself might be having issues. Run:
     ```bash
     sudo systemctl status docker
     ```
   - If the Docker service isn't running, try starting it with:
     ```bash
     sudo systemctl start docker
     ```

3. **Test DNS resolution**:
   - If there's a DNS issue, Docker may have trouble reaching the registry. Try running:
     ```bash
     nslookup registry-1.docker.io
     ```
   - If the DNS resolution fails, try configuring a different DNS server (like Google's 8.8.8.8) in your Docker daemon settings.

4. **Increase Docker timeout settings**:
   - The error message mentions a timeout issue while waiting for the connection. You can try increasing Docker's default timeout by editing the Docker daemon configuration:
     - Edit `/etc/docker/daemon.json` (create it if it doesn't exist).
     - Add or modify the following configuration:
       ```json
       {
         "registry-mirrors": ["https://registry-1.docker.io"],
         "debug": true,
         "log-level": "debug"
       }
       ```
     - Restart Docker after modifying the configuration:
       ```bash
       sudo systemctl restart docker
       ```

5. **Check Docker Hub status**:
   - It's also possible that Docker Hub is experiencing issues. You can check the [Docker status page](https://status.docker.com/) to confirm.

6. **Try pulling the image manually**:
   - Instead of running `docker run hello-world`, try pulling the image manually first:
     ```bash
     docker pull hello-world
     ```
   - This will give you more detailed error messages, which might help with further diagnosis.

7. **Check firewall or proxy settings**:
   - If you're behind a proxy or firewall, make sure Docker is configured to work with it. You may need to set proxy settings for Docker. You can do this by editing `/etc/systemd/system/docker.service.d/http-proxy.conf` and adding the following:
     ```ini
     [Service]
     Environment="HTTP_PROXY=http://your-proxy:port"
     Environment="HTTPS_PROXY=http://your-proxy:port"
     ```
   - After adding the settings, restart Docker:
     ```bash
     sudo systemctl daemon-reload
     sudo systemctl restart docker
     ```

8. **Reinstall Docker**:
   - If the above steps don’t work, you can try reinstalling Docker. First, uninstall it:
     ```bash
     sudo apt-get remove docker docker-engine docker.io containerd runc
     ```
   - Then follow the official installation guide for your platform to reinstall Docker.

After following these steps, try running `docker run hello-world` again and check if the issue is resolved.