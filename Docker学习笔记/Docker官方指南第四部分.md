### 先决条件

- 安装Docker版本1.13或更高版本。 
- 按照第3部分的先决条件中所述获取Docker compose。 
- 取预装Docker for Mac和Docker for Windows的Docker Machine，但在Linux系统上需要直接安装它。在没有Hyper-V的Windows 10系统之前以及Windows 10 Home中，使用Docker Toolbox。 
- 阅读第1部分中的取向 
- 学习如何在第2部分中创建容器。 
- 确保您已发布通过推送到注册表创建的friendlyhello图像。我们在这里使用该共享图像。 
- 确保你的图像作为一个部署的容器。运行这个命令，在你的信息中插入用户名，repo和标签：docker run -p 80:80 username/repo:tag ，然后访问http://localhost/ 。
- 方便地从第3部分复制docker-compose.yml。 

### 介绍

在第3部分中，您介绍了您在第2部分中编写的应用程序，并定义了它应该如何在生产环境中运行，将其转化为服务，并在此过程中将其扩展5倍。 

在第4部分中，您将此应用程序部署到群集上，并在多台机器上运行它。通过将多台机器连接到称为群集的“Dockerized”群集，使多容器，多机器应用成为可能。 

### 了解Swarm集群 

Swarm是一组运行Docker并加入到集群中的机器。发生这种情况后，您将继续运行您习惯的Docker命令，但现在它们将由群集管理器在群集上执行。群体中的机器可以是物理的或虚拟的。加入群体后，他们被称为节点。 

Swarm管理人员可以使用多种策略来运行容器，例如“最空节点” - 它可以使用容器填充使用率最低的机器。或者“全局”，它确保每台机器只获取指定容器的一个实例。您指示swarm经理在Compose文件中使用这些策略，就像您已经使用的策略一样。 

群体管理者是群体中唯一可以执行你的命令的机器，或者授权其他机器作为工作者加入群体。工人只是在那里提供能力，并没有权力告诉任何其他机器可以做什么和不可以做什么。 

到目前为止，您已经在本地机器上以单主机模式使用Docker。但是Docker也可以切换到群集模式，这就是使用群集的原因。立即启用群模式使当前的机器成为群管理器。从此，Docker将运行您在您管理的群集上执行的命令，而不仅仅是在当前机器上执行。 

### 建立你的群

一个群体由多个节点组成，可以是物理机器或虚拟机器。基本概念很简单：运行docker swarm init来启用swarm模式，并让你的当前机器成为swarm manager，然后在其他机器上运行docker swarm join，让它们作为工作者加入swarm。 

选择下面的选项卡，看看它是如何在各种情况下发挥作用的。我们使用虚拟机快速创建一个双机群集，并将其变成群集。  

#### 创建一个集群 

##### **VMS在本地计算机上（MAC，LINUX，WINDOWS 7和8）** 

您需要可以创建虚拟机（VM）的虚拟机管理程序，因此请为您的计算机的操作系统安装Oracle VirtualBox。 

> 注意：如果您位于安装了Hyper-V的Windows系统（如Windows 10）上，则无需安装VirtualBox，而应该使用Hyper-V。通过单击上面的Hyper-V选项卡查看Hyper-V系统的说明。如果你使用的是Docker Toolbox，你应该已经安装了VirtualBox作为它的一部分，所以你很好。 
>
> **Docker Toolbox:安装后打开桌面快捷启动图标会提示下载boot2docker.iso，可以直接手动下载下来放入C:\Users\jason\.docker\machine\cache目录下**

现在，使用Docker-machine创建几个虚拟机，使用VirtualBox驱动程序： 

```
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```

##### **VMS在本地机器上（WINDOWS 10）** 

首先，快速为您的虚拟机（VM）创建一个虚拟交换机以便共享，以便它们可以相互连接。 

1. 启动Hyper-V管理器 
2. 点击右侧菜单中的虚拟交换管理器 
3. 单击创建类型为External的虚拟交换机 
4. 将它命名为myswitch，然后选中复选框以共享主机的活动网络适配器 

现在，使用我们的节点管理工具docker-machine创建几个虚拟机： 

```
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm1
docker-machine create -d hyperv --hyperv-virtual-switch "myswitch" myvm2
```

##### 列出VMS并获取他们的IP地址 

您现在创建了两个名为myvm1和myvm2的虚拟机。 

使用此命令列出机器并获取其IP地址。 

```
docker-machine ls
```

这里是这个命令的输出示例。 

```
$ docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
default   *        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce
myvm1     -        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce
myvm2     -        virtualbox   Running   tcp://192.168.99.102:2376           v18.05.0-ce
```

##### 初始化群集并添加节点 

第一台机器作为管理员，执行管理命令并认证工人加入群体，第二台机器是工人。 

您可以使用docker-machine ssh将命令发送到您的VM。指示myvm1成为一个拥有docker swarm init的swarm manager并寻找这样的输出： 

```
$ docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.101"
Swarm initialized: current node (sd88kjp8xd27wx7ozce2v7qo3) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-54nv19pp4b5z1kcrcauc18hfu9t6435gtauidf84cffmitmqgd-67irvhe81j5dzqnuyl8v3xi7d 192.168.99.101:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

> 端口 2377 和2376
>
> 始终使用端口2377（群管理端口）运行docker swarm init和docker swarm join，或根本不运行端口，并让其采用默认值。 
>
> 由docker-machine ls返回的计算机IP地址包括端口2376，它是Docker守护程序端口。请勿使用此端口，否则可能会遇到错误。 

> 无法使用SSH？试试--native-ssh标志 
>
> 如果由于某些原因，您无法将命令发送给Swarm管理器，Docker Machine可以选择让您使用自己的系统的SSH。只需在调用ssh命令时指定--native-ssh标志： 
>
> ```
> docker-machine --native-ssh ssh myvm1 ...
> ```

如您所见，对docker swarm init的响应包含一个预配置的docker swarm join命令，您可以在要添加的任何节点上运行该命令。复制这个命令，并通过docker-machine ssh将它发送到myvm2，让myvm2作为一个worker加入你的新群体： 

```
$ docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-54nv19pp4b5z1kcrcauc18hfu9t6435gtauidf84cffmitmqgd-67irvhe81j5dzqnuyl8v3x
i7d 192.168.99.101:2377" # 在上面执行完docker-machine ssh myvm1 "docker swarm init --advertise-addr 192.168.99.101"会提示这句命令
# 结果如下：
$ docker-machine ssh myvm2 "docker swarm join --token SWMTKN-1-54nv19pp4b5z1kcrcauc18hfu9t6435gtauidf84cffmitmqgd-67irvhe81j5dzqnuyl8v3x
i7d 192.168.99.101:2377"
This node joined a swarm as a worker.
```

恭喜，你已经创建了你的第一个群！ 

在管理器上运行docker node ls  以查看此群中的节点： 

```
$ docker-machine ssh myvm1 "docker node ls"
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
vws0j2o8yogdq2itug7ookxrv     default             Ready               Active                                  18.05.0-ce
sd88kjp8xd27wx7ozce2v7qo3 *   myvm1               Ready               Active              Leader              18.05.0-ce
81bc2g3uxpytgykor0r73g3e9     myvm2               Ready               Active                                  18.05.0-ce
```

> 离开一个群 
>
> 如果你想重新开始，你可以从每个节点运行docker swarm离开。 
>
> $ docker-machine ssh default "docker swarm leave"
> Node left the swarm.
>
> ```
> $ docker-machine ssh myvm1 "docker node ls"
> ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
> vws0j2o8yogdq2itug7ookxrv     default             Down                Active                                  18.05.0-ce
> sd88kjp8xd27wx7ozce2v7qo3 *   myvm1               Ready               Active              Leader              18.05.0-ce
> 81bc2g3uxpytgykor0r73g3e9     myvm2               Ready               Active                                  18.05.0-ce
> ```

### 在群集上部署您的应用程序 

困难的部分结束了。现在，您只需重复第3部分中用于部署新流程的流程即可。请记住，只有像myvm1这样的群集管理器才能执行Docker命令;工人只是为了能力。 

#### 为swarm管理器配置docker-machine shell 

到目前为止，您已经在Docker-machine ssh中将Docker命令包装为与虚拟机交谈。另一种选择是运行docker-machine env <machine>来获取并运行一个命令，该命令将当前shell配置为与VM上的Docker守护进程进行通信。 此方法对下一步更好，因为它允许您使用本地docker-compose.yml文件“远程”部署应用程序，而无需将其复制到任何位置。 

键入docker-machine env myvm1，然后复制粘贴并运行作为输出最后一行提供的命令，以将shell配置为与swarm管理器myvm1对话。 

配置shell的命令根据你是Mac，Linux还是Windows而有所不同，因此下面的选项卡中显示了每个命令的示例。 

##### MAC或LINUX上的DOCKER MACHINE SHELL环境 

运行docker-machine env myvm1以获取配置shell以与myvm1进行通信的命令。 

```
$ docker-machine env myvm1
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/sam/.docker/machine/machines/myvm1"
export DOCKER_MACHINE_NAME="myvm1"
# Run this command to configure your shell:
# eval $(docker-machine env myvm1)
```

运行给定的命令来配置你的shell与myvm1进行通信。 

```
eval $(docker-machine env myvm1)
```

运行docker-machine ls以验证myvm1现在是活动机器，如旁边的星号所示。 

```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce   
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce   
```

##### WINDOWS上的DOCKER MACHINE SHELL环境 

运行docker-machine env myvm1以获取配置shell以与myvm1进行通信的命令。 

```
PS C:\Users\sam\sandbox\get-started> docker-machine env myvm1
$Env:DOCKER_TLS_VERIFY = "1"
$Env:DOCKER_HOST = "tcp://192.168.203.207:2376"
$Env:DOCKER_CERT_PATH = "C:\Users\sam\.docker\machine\machines\myvm1"
$Env:DOCKER_MACHINE_NAME = "myvm1"
$Env:COMPOSE_CONVERT_WINDOWS_PATHS = "true"
# Run this command to configure your shell:
# & "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env myvm1 | Invoke-Expression
```

注：Windows 10 安装的Docker Toolbox下执行结果如下：

```
$ docker-machine env myvm1
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.101:2376"
export DOCKER_CERT_PATH="C:\Users\geekview\.docker\machine\machines\myvm1"
export DOCKER_MACHINE_NAME="myvm1"
export COMPOSE_CONVERT_WINDOWS_PATHS="true"
# Run this command to configure your shell:
# eval $("C:\Program Files\Docker Toolbox\docker-machine.exe" env myvm1)
```

运行给定的命令来配置你的shell与myvm1进行通信。 

```
& "C:\Program Files\Docker\Docker\Resources\bin\docker-machine.exe" env myvm1 | Invoke-Expression
Windows 10 安装的Docker Toolbox下执行：eval $("C:\Program Files\Docker Toolbox\docker-machine.exe" env myvm1)
```

运行docker-machine ls以验证myvm1是否为活动机器，如旁边的星号所示。 

```
$ docker-machine ls
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
default   -        virtualbox   Running   tcp://192.168.99.100:2376           v18.05.0-ce
myvm1     *        virtualbox   Running   tcp://192.168.99.101:2376           v18.05.0-ce
myvm2     -        virtualbox   Running   tcp://192.168.99.102:2376           v18.05.0-ce
```

### 在swarm管理器上部署应用程序 

现在您已拥有myvm1，您可以使用其权力作为swarm管理器，通过使用第3部分中用于myvm1的相同docker stack deploy命令和docker-compose.yml的本地副本来部署您的应用程序。 该命令可能需要几秒钟才能完成，部署需要一段时间才能完成。 在swarm管理器上使用docker service ps <service_name>命令验证所有服务是否已被重新部署。 

您通过docker-machine shell配置连接到myvm1，并且您仍然可以访问本地主机上的文件。确保你和之前在同一个目录下，其中包括你在第3部分中创建的docker-compose.yml文件。 

就像以前一样，运行以下命令在myvm1上部署应用程序。 

```
docker stack deploy -c docker-compose.yml getstartedlab
```

就是这样，该应用程序部署在swarm集群上！ 

> 注意：如果您的映像存储在私有注册表而不是Docker Hub中，则需要使用docker login <your-registry>登录，然后您需要将--with-registry-auth标志添加到上述命令中。例如： 
>
> ```
> docker login registry.example.com
> 
> docker stack deploy --with-registry-auth -c docker-compose.yml getstartedlab
> ```
>
> 这使用加密的WAL日志将登录令牌从本地客户端传递到部署服务的群集节点。有了这些信息，这些节点就能够登录到注册表并提取图像。 

现在，您可以使用第3部分中使用的相同docker命令。只有这一次，请注意，服务（及相关容器）已在myvm1和myvm2之间分配。 









[原文链接](https://docs.docker.com/get-started/part4/)



