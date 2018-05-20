先决条件 

- 安装Docker版本1.13或更高版本。 

- 获取 [Docker Compose](https://docs.docker.com/compose/overview/) 。在Docker for Mac和Docker for Windows中，它已预先安装，所以你很好用。在Linux系统上，您需要[直接安装它](https://docs.docker.com/compose/install/#install-compose)。在没有Hyper-V的Windows 10系统之前，使用Docker Toolbox。 

  > 安装Docker Compose:
  >
  > ```
  > sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose # 注意修改版本号
  > sudo chmod +x /usr/local/bin/docker-compose
  > ```
  >
  > 测试
  >
  > ```
  > $ docker-compose --version
  > docker-compose version 1.21.2, build 1719ceb
  > ```

- 阅读第1部分中的取向。 

- 学习如何在第2部分中创建容器。 

- 确保您已发布通过推送到注册表创建的friendlyhello镜像。我们在这里使用该共享镜像。 

- 确保你的镜像作为一个部署的容器。运行这个命令，在你的信息中插入用户名，repo和标签：docker run -p 80:80 username/repo:tag ，然后访问http://localhost/ 。 

### 介绍

在第3部分中，我们扩展了我们的应用并实现了负载平衡。要做到这一点，我们必须在分布式应用程序的层次结构中升级一级：服务。 

- Stack
- **Services** (you are here)
- Container (covered in [part 2](https://docs.docker.com/get-started/part2/))

### 关于服务 （**Services** ）

在分布式应用程序中 ，不同的应用程序被称为“服务”。 例如，如果您想象一个视频共享网站，那么它可能包含用于将应用程序数据存储在数据库中的服务，用户上传内容后的视频转码服务，前端服务等。 

服务真的只是“生产中的容器”。 一个服务只运行一个镜像，但它编码镜像运行的方式 - 它应该使用哪个端口，容器应运行多少个副本，以便服务具有所需的容量等等。 缩放服务会更改运行该软件的容器实例的数量，从而为流程中的服务分配更多计算资源。 

幸运的是，使用Docker平台定义，运行和扩展服务非常简单 - 只需编写一个docker-compose.yml文件即可。 

### 你的第一个docker-compose.yml文件 

docker-compose.yml文件是一个YAML文件，它定义了Docker容器在生产中的行为方式。 

#### docker-compose.yml

将此文件保存为docker-compose.yml，无论你在哪里。确保您已将第2部分中创建的图像推送到注册表中，并通过用您的镜像细节替换username / repo：标签来更新此.yml。 

```
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "80:80"
    networks:
      - webnet
networks:
  webnet:
```

这个docker-compose.yml文件告诉Docker执行以下操作： 

- 从注册表中拉出我们在步骤2中上传的镜像。 
- 运行该图像的5个实例作为一个名为web的服务，限制每个实例使用最多10％的CPU（跨所有核心）和50MB的RAM。 
- 如果一个失败，立即重启容器。 
- 将主机上的端口80映射到Web的端口80。 
- 指示web容器通过称为webnet的负载平衡网络共享端口80。 （在内部，容器本身在临时端口上发布到web的端口80）。 
- 使用默认设置（这是一个负载平衡覆盖网络）定义webnet网络。 

### 运行新的负载平衡应用程序 

在我们可以使用docker stack deploy命令之前，我们首先运行： 

```
jason@jason:~$ sudo docker swarm init
Swarm initialized: current node (9f8739wij0a30pis7p31ayr96) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-1lslx34gyz5y0i4o03nuel6xtyiitvfszlw6y8zcqxhq5d8wty-6dst29gykjec4rz1zr1x21lq4 10.0.2.15:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

> 注意：我们在第4部分中介绍了该命令的含义。如果不运行docker swarm init，则会出现“此节点不是swarm manager”的错误。 

现在我们来运行它。你需要给你的应用一个名字。在这里，它被设置为getstartedlab： 

```
jason@jason:~$ sudo docker stack deploy -c docker-compose.yml getstartedlab
Creating network getstartedlab_webnet
Creating service getstartedlab_web
jason@jason:~$ 
```

我们的单个服务堆栈在一台主机上运行了5个部署映像的容器实例。让我们来调查。 

在我们的应用程序中获取一项服务的服务ID： 

```
jason@jason:~$ sudo docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                          PORTS
0g8dk2zz9jrk        getstartedlab_web   replicated          5/5                 3217832740/get-started:part2   *:80->80/tcp
```

查找Web服务的输出，并以您的应用程序名称作为前缀。如果您将其命名为与此示例中所示的相同，则名称为getstartedlab_web。还列出了服务ID以及副本数量，镜像名称和端口暴露量。 

在服务中运行的单个容器称为任务(Task)。任务会获得数值增加的唯一ID，最大数量为您在docker-compose.yml中定义的副本数量。列出您的服务的任务： 

```
jason@jason:~$ sudo docker service ps getstartedlab_web
ID                  NAME                  IMAGE                          NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
51oceskpvqe8        getstartedlab_web.1   3217832740/get-started:part2   jason               Running             Running 3 minutes ago                       
w3hyuhob0lmw        getstartedlab_web.2   3217832740/get-started:part2   jason               Running             Running 3 minutes ago                       
ywj7gjs3hdeu        getstartedlab_web.3   3217832740/get-started:part2   jason               Running             Running 3 minutes ago                       
klg5lb6j6ulg        getstartedlab_web.4   3217832740/get-started:part2   jason               Running             Running 3 minutes ago                       
zc866sd6gouh        getstartedlab_web.5   3217832740/get-started:part2   jason               Running             Running 3 minutes ago  
```

如果您只列出了系统中的所有容器，但任务也显示出来，尽管这不是按服务过滤的： 

```
jason@jason:~$ sudo docker container ls -q
4985bb6c8a03
9ddb76680ab3
287c1c2c121f
6391b4f400e0
78d86d52c18f
```

您可以连续多次运行curl -4 http：// localhost，或者在浏览器中转到该URL并刷新几次。 

无论哪种方式，容器ID都会发生变化，从而显示负载平衡;在每个请求中，以循环方式选择5个任务中的一个来响应。容器ID与前一个命令（docker container ls -q）的输出相匹配。 

>在Windows10上运行
>
>Windows 10 PowerShell应该已经有curl可用了，但是如果没有，你可以抓取一个像Git BASH这样的Linux终端模拟器，或者下载非常相似的wget for Windows。 

> 根据您的环境的网络配置，容器可能需要长达30秒才能响应HTTP请求。这并不代表Docker或群集性能，而是我们稍后在本教程中讨论的未满足的Redis依赖项。就目前而言，访客柜台并不是出于同样的原因;我们还没有添加服务来保存数据。 

### 扩展应用程序 

您可以通过更改docker-compose.yml中的replicas 的值（将replicas 由原来的5改8之后：），保存更改并重新运行docker stack deploy命令来扩展应用程序： 

```
jason@jason:~$ sudo docker stack deploy -c docker-compose.yml getstartedlab
Updating service getstartedlab_web (id: 0g8dk2zz9jrktg3rpomrkun26)
jason@jason:~$ sudo docker service ps getstartedlab_web
ID                  NAME                  IMAGE                          NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
51oceskpvqe8        getstartedlab_web.1   3217832740/get-started:part2   jason               Running             Running 14 minutes ago                       
w3hyuhob0lmw        getstartedlab_web.2   3217832740/get-started:part2   jason               Running             Running 14 minutes ago                       
ywj7gjs3hdeu        getstartedlab_web.3   3217832740/get-started:part2   jason               Running             Running 14 minutes ago                       
klg5lb6j6ulg        getstartedlab_web.4   3217832740/get-started:part2   jason               Running             Running 14 minutes ago                       
zc866sd6gouh        getstartedlab_web.5   3217832740/get-started:part2   jason               Running             Running 14 minutes ago                       
g18h1lugisvw        getstartedlab_web.6   3217832740/get-started:part2   jason               Running             Running 7 minutes ago                        
vd6m434zj8qo        getstartedlab_web.7   3217832740/get-started:part2   jason               Running             Running 7 minutes ago                        
1imnpc8b53uj        getstartedlab_web.8   3217832740/get-started:part2   jason               Running             Running 7 minutes ago  
jason@jason:~$ 
```

Docker执行一个就地更新，不需要先撕下堆栈或杀死任何容器。 

现在，重新运行docker container ls -q以查看重新配置的已部署实例。如果您扩大副本，则会启动更多任务，因此还会启动更多容器。 

#### 取下应用程序和群 

- 通过docker stack rm关闭应用 ： 

  ```
  jason@jason:~$ sudo docker stack rm getstartedlab
  Removing service getstartedlab_web
  Removing network getstartedlab_webnet
  ```

- 关闭集群

  ```
  jason@jason:~$ sudo docker swarm leave --force
  Node left the swarm.
  ```

  用Docker站起来并扩展您的应用程序非常简单。您已经朝着学习如何在生产中运行容器迈出了一大步。接下来，您将学习如何将这个应用程序作为Docker机器群集上的真正群体运行。 

  > 注意：像这样编写文件用于使用Docker定义应用程序，并且可以使用Docker Cloud将其上传到云提供商，或者使用Docker Enterprise Edition选择的任何硬件或云提供商。 

   

### 总结回顾

总而言之，在输入docker run 时非常简单，生产中容器的真正实现就是将其作为服务运行。服务在Compose文件中编写容器的行为，此文件可用于缩放，限制和重新部署我们的应用程序。对服务的更改可以在运行时适用，使用启动服务的相同命令：docker stack deploy。 

现阶段需要探索的一些命令： 

```
docker stack ls                                            	# 列出堆栈或应用程序
docker stack deploy -c <composefile> <appname>  		# 运行指定的Compose文件
docker service ls                 				# 列出与应用关联的正在运行的服务
docker service ps <service>                  			 # 列出与应用关联的任务
docker inspect <task or container>                   		   # 检查任务或容器
docker container ls -q                                      	   # 列出容器ID
docker stack rm <appname>                             		  # 关闭一个应用程序
docker swarm leave --force      					 # 从manager身上取下一个节点群
```



[原文链接](https://docs.docker.com/get-started/part3)