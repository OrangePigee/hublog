---
title: "Docker MineCraft Server"
date: 2022-01-26T19:12:43+08:00
lastmod: 2022-01-26T19:12:43+08:00
draft: false
tags: ['docker','minecraft']
categories: []
author: ""

---

## Docker容器技术搭建MineCraft服务器

## Java版服务器(minecraft-server)

1. Docker Hub上寻找镜像

	https://hub.docker.com/r/itzg/minecraft-server

2. 拉取镜像


   ```bash
   docker pull itzg/minecraft-server
   ```


3. 请配合官方文档食用

   This docker image provides a Minecraft Server that will automatically download the latest stable version at startup. You can also run/upgrade to any specific version or the latest snapshot. See the *Versions* section below for more information.

   To simply use the latest stable version, run

   ```bash
   docker run -d -it -p 25565:25565 -e EULA=TRUE itzg/minecraft-server
   ```

4. 上面的这种方式默认使用最新的MC(MineCraft)Server版本，如果想要自定义版本（在下面的VERSION中替换想要的版本即可，例 VERSION=1.7.9）
   ```bash
   docker run -d -it -p 25565:25565 -e EULA=TRUE  -e VERSION=1.7.9 itzg/minecraft-server
   ```

5. 如果想要配置更多MineCraft服务器相关信息，不要忘记 -v 参数，他可以把容器中的卷挂在到你主机的磁盘上

   ```
   -v /home/user/minecraft-data:/data
   ```

   > 使用前务必检查根目录下有没有创建data目录，以及权限问题，docker最好单独分出来一个用户组，或把管理员用户加入docker组，不用滥用root (sudo) 权限

   #### Docker-compose

   ```yaml
    version: "3"

    services:
      mc:
        image: itzg/minecraft-server
        ports:
          - 25565:25565
        environment:
          EULA: "TRUE"
        tty: true
        stdin_open: true
        restart: unless-stopped
        volumes:
          # attach a directory relative to the directory containing this compose file
          - ./minecraft-data:/data
   ```

## 基岩版服务器(minecraft-bedrock-server)

1. Docker Hub 镜像

   https://hub.docker.com/r/itzg/minecraft-bedrock-server

2. 拉取镜像

   ```bash
   docker pull itzg/minecraft-bedrock-server
   ```

3. 配置官方文档食用

   The following starts a Bedrock Dedicated Server running a default version and exposing the default UDP port:

   ```bash
   docker run -d -it -e EULA=TRUE -p 19132:19132/udp itzg/minecraft-bedrock-server
   ```

4. 完成上面三步就可以跑起来了，如果需要配置额外服务器相关，见上述Java版服务器第 4、5 步

5. Kubernetes

   ```yaml
   env:
   - name: EULA
     value: "TRUE"
   - name: GAMEMODE
     value: survival
   - name: DIFFICULTY
     value: normal
   ```

   ```bash
   kubectl apply -f examples/kubernetes.yml
   ```

   ```bash
   kubectl logs -f deployment/bds
   ```

   > Docker-compose与Kubernetes任君选择

