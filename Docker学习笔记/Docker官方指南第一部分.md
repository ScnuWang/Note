国内镜像：

```
https://docker.mirrors.ustc.edu.cn
https://registry.docker-cn.com
```

[官方文档](https://docs.docker.com/get-started/) 版本：18.03

## Docker相关概念

Docker是一个可以让开发者和系统管理员以容器的方式开发、部署、运行应用的平台。使用Linux容器来部署应用程序称为集装箱化。容器不是新的，但它们用于轻松部署应用程序。

集装箱化越来越受欢迎，因为集装箱是：

- 灵活：即使是最复杂的应用程序也可以装箱。
- 轻量级：容器利用并共享主机内核。
- 可互换：您可以即时部署更新和升级。
- 便携式：您可以在本地构建，部署到云中并在任何地方运行。
- 可扩展性：您可以增加和自动分发容器副本。
- 可堆叠：您可以垂直堆叠服务并即时堆叠服务。

#### 镜像（Image）和容器(containers)

通过运行镜像启动容器。镜像是一个可执行程序包，包含运行应用程序所需的所有内容 - 代码，运行时，库，环境变量和配置文件。

容器是镜像的运行时实例----执行时镜像在内存中的内容（即具有状态的镜像或用户进程）。您可以使用命令docker ps查看正在运行的容器的列表，就像在Linux中一样。

#### 容器和虚拟机

一个容器在Linux上本地运行，并与其他容器共享主机的内核。它运行一个独立的进程，不占用任何其他可执行文件的内存，使其轻量化。

相比之下，虚拟机（VM）运行一个完整的“客户”操作系统，通过虚拟机管理程序虚拟访问主机资源。一般来说，虚拟机提供的环境比大多数应用程序需要的资源更多。

## 准备你的Docker环境

在支持的平台上安装Docker Community Edition（CE）或企业版（EE）的维护版本。

1. 安装Docker（Ubuntu 18.04 LTS）：

   ```
   sudo apt install docker.io
   ```

2. 测试

   ```
   docker --version # 查看版本
   sudo docker info # 查看很多信息，包括版本、操作系统版本等相关信息
   ```

## 安装Hello-World

1. 通过运行简单的Docker镜像hello-world来测试您的安装是否工作正常：

   ```
   jason@jason:/etc/docker$ sudo docker run hello-world
   Unable to find image 'hello-world:latest' locally
   latest: Pulling from library/hello-world
   9bb5a5d4561a: Pull complete 
   Digest: sha256:f5233545e43561214ca4891fd1157e1c3c563316ed8e237750d59bde73361e77
   Status: Downloaded newer image for hello-world:latest

   Hello from Docker!
   This message shows that your installation appears to be working correctly.
   ```

2. 列出下载到您的机器上的hello-world镜像：

   ```
   jason@jason:/etc/docker$ sudo docker image ls
   REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
   hello-world         latest              e38bc07ac18e        5 weeks ago         1.85kB
   ```

3. 列出显示消息后退出的hello-world容器（由镜像产生）。如果它仍在运行，则不需要--all选项：

   ```
   jason@jason:/etc/docker$ sudo docker container ls --all
   CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
   2cda74f33099        hello-world         "/hello"            3 minutes ago       Exited (0) 3 minutes ago                       priceless_jennings
   ```

## 命令总结

```
## 列出Docker CLI命令
docker
docker container --help

## 查询Docker版本信息
docker --version
docker version
docker info

## 执行Docker镜像
docker run hello-world

## 列出Docker镜像
docker image ls

## 列出Docker容器 (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```

## 本部分总结

容器化使 [CI/CD](https://www.docker.com/use-cases/cicd)无缝。例如：

- 应用程序没有系统依赖关系
- 更新可以推送到分布式应用程序的任何部分
- 资源密度可以被优化。

使用Docker，扩展应用程序的过程就是启动新的可执行文件，而不是运行繁重的VM主机。

[原文链接](https://docs.docker.com/get-started/)