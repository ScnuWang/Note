### 先决条件

- 安装Docker版本1.13或更高版本。

- 阅读第1部分中的方向。

- 让您的环境快速测试运行，以确保您全部设置完毕：

  ```
  docker run hello-world
  ```

### 介绍

现在是开始以Docker方式构建应用程序的时候了。我们从这种应用程序的层次结构的底部开始，这是一个容器，我们将在此页面上介绍。在这个层次上面是一个服务，它定义了容器在生产中的行为方式，如第3部分所述。最后，在顶层是堆栈，定义了第5部分中涵盖的所有服务的交互。

- Stack
- Services
- **Container** (you are here)

### 您的新开发环境

过去，如果您要开始编写Python应用程序，您的第一个业务就是将Python运行时安装到您的机器上。但是，这会造成您的计算机上的环境需要完美适合您的应用程序按预期运行，并且还需要与您的生产环境相匹配。

使用Docker，您可以将一个可移植的Python运行时作为一个映像获取，无需安装。然后，您的构建可以将基础Python镜像与应用程序代码一起包括在内，确保您的应用程序，依赖项和运行时都一起旅行。

这些可移植的镜像是由称为Dockerfile的文件定义的。

### 用Dockerfile定义一个容器

Dockerfile定义了容器内环境中发生的事情。访问网络接口和磁盘驱动器等资源是在此环境中虚拟化的，与系统的其余部分隔离，因此您需要将端口映射到外部世界，并明确要将哪些文件“复制”到那个环境。但是，在完成这些之后，您可以预期在此Dockerfile中定义的应用程序的构建在运行时的行为完全相同。

#### Dockerfile

创建一个空目录。将目录（cd）更改为新目录，创建一个名为Dockerfile的文件，将以下内容复制并粘贴到该文件中并保存。注意解释新Dockerfile中每条语句的注释。

```
# 使用一个官方的Python运行时作为父镜像
FROM python:2.7-slim

# 设置 /app 为工作目录
WORKDIR /app

# 将当前目录内容复制到/ app的容器中
ADD . /app

# 安装requirements.txt中指定的所有必需软件包
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# 在此容器外向全球提供端口80
EXPOSE 80

# 定义环境变量
ENV NAME World

# 容器启动时运行app.py
CMD ["python", "app.py"]
```

这个Dockerfile引用了一些我们还没有创建的文件，分别是app.py和requirements.txt。接下来创建这些。

### 应用程序本身

创建另外两个文件，requirements.txt和app.py，并将它们与Dockerfile放在同一个文件夹中。这完成了我们的应用程序，您可以看到它非常简单。当上面的 Dockerfile 内置到镜像中时，由于Dockerfile的ADD命令，app.py和requirements.txt存在，并且app.py的输出可通过HTTP访问，这要归功于EXPOSE命令。

#### `requirements.txt`

```
Flask
Redis
```

#### `app.py`

```
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

现在我们看到pip install -r requirements.txt为Python安装Flask和Redis库，并且该应用程序输出环境变量NAME以及调用socket.gethostname（）的输出。最后，因为Redis没有运行（因为我们只安装了Python库，而不是Redis本身），所以我们应该期望在这里使用它的尝试失败并产生错误消息。

>注意：在容器中访问主机的名称将检索容器ID，这与正在运行的可执行文件的进程ID相似。

而已！您的系统上不需要Python或任何requirements.txt文件，也不需要在您的系统上安装或运行此映像。看起来你并没有真正用Python和Flask建立一个环境，但是你已经拥有了。

### 构建应用程序

我们准备构建应用程序。确保您仍然处于新目录的顶层。以下是ls应该显示的内容：

```
$ ls
Dockerfile		app.py			requirements.txt
```

现在运行build命令。这会创建一个Docker映像，我们将使用-t标记它，因此它有一个友好名称。

```
docker build -t friendlyhello .
```

你的构建的镜像在哪里？它在你的机器的本地Docker镜像注册表中：

```
$ docker image ls

REPOSITORY            TAG                 IMAGE ID
friendlyhello         latest              326387cea398
```

>针对Linux用户的故障排除:
>
>**DNS设置**
>
>代理服务器在启动并运行后可以阻止与您的网络应用程序的连接。如果您位于代理服务器的后面，请使用ENV命令为您的代理服务器指定主机和端口，将以下行添加到Dockerfile中：
>
>```
># 设置代理服务器，将host：port替换为服务器的值
>ENV http_proxy host:port
>ENV https_proxy host:port
>```
>
>**代理服务器设置**
>
>DNS错误配置可能会导致pip出现问题。您需要设置您自己的DNS服务器地址以使pip正常工作。您可能需要更改Docker守护程序的DNS设置。您可以使用dns键在/etc/docker/daemon.json编辑（或创建）配置文件，如下所示：
>
>```
>{
>  "dns": ["your_dns_address", "8.8.8.8"]
>}
>```
>
>在上面的例子中，列表的第一个元素是你的DNS服务器的地址。第二项是Google的DNS，当第一项不可用时可以使用它。
>
>在继续之前，保存daemon.json并重新启动docker服务：**sudo service docker restart**
>
>修复后，重试运行构建命令。

### 运行应用程序

运行应用程序，使用-p将机器的端口4000映射到容器的已发布端口80：

```
docker run -p 4000:80 friendlyhello
```

你应该在http://0.0.0.0:80看到一条消息，Python正在为你的应用程序提供服务。但是该消息来自容器内部，它不知道您将该容器的端口80映射到4000，从而制作正确的URL http://localhost:4000/。结果如下： 

```
Hello World!
Hostname: 3107aee4112c
Visits: cannot connect to Redis, counter disabled
```

> 注意：如果您在Windows 7上使用Docker Toolbox，请使用Docker Machine IP而不是本地主机。例如，http://192.168.99.100:4000/。要查找IP地址，请使用命令docker-machine ip。

您也可以在shell中使用curl命令来查看相同的内容。

```
$ curl http://localhost:4000

<h3>Hello World!</h3><b>Hostname:</b> 3107aee4112c<br/><b>Visits:</b> <i>cannot connect to Redis, counter disabled</i>
```

这个4000：80的端口重映射是为了演示Dockerfile中的EXPOSE与使用docker run -p发布的内容之间的区别。在后面的步骤中，我们只需将主机上的端口80映射到容器中的端口80并使用http:// localhost。 在终端中点击CTRL + C退出。

> 在Windows上，显式停止容器
>
> 在Windows系统上，CTRL + C不会停止容器。因此，首先键入CTRL + C以获取提示（或打开另一个shell），然后键入docker container ls列出正在运行的容器，然后按docker container stop <Container NAME或ID>停止容器。否则，当您尝试在下一步中重新运行容器时，会从守护程序中收到错误响应。

现在让我们以分离模式在后台运行应用程序：

```
docker run -d -p 4000:80 friendlyhello
```

结果如下：

```
Hello World!
Hostname: 8ab5415c4959
Visits: cannot connect to Redis, counter disabled
```

您可以获取应用的长容器ID，然后将其踢回到您的终端。您的容器正在后台运行。您还可以使用docker container ls查看缩写的容器ID（并且在运行命令时都可以互换使用）：

```
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED
8ab5415c4959        friendlyhello       "python app.py"     28 seconds ago
```

请注意，CONTAINER ID与http://localhost:4000/上的内容匹配。

现在使用docker container stop来结束进程，使用CONTAINER ID，如下所示：

```
jason@jason:~/dockerpackage$ sudo docker container stop 8ab5415c4959
8ab5415c4959
```

### 分享你的镜像

为了演示我们刚才创建的可移植性，我们上传我们构建的映像并在其他地方运行它。毕竟，当您想要将容器部署到生产环境时，您需要知道如何推送注册表。

注册表是存储库的集合，而存储库是图像的集合 - 有点像GitHub存储库，但代码已经创建。注册表上的帐户可以创建许多存储库。 docker CLI默认使用Docker的公共注册表。

> 注意：我们在这里使用Docker的公共注册表仅仅是因为它是免费和预先配置的，但有许多公共选项可供选择，甚至可以使用Docker Trusted Registry设置您自己的私有注册表。

#### 使用您的Docker ID登录

如果您没有Docker帐户，请在[cloud.docker.com](https://cloud.docker.com/)注册一个帐户。记下你的用户名。

登录到本地计算机上的Docker公共注册表。

```
$ docker login
```

#### 标记镜像

将本地镜像与注册表中的存储库相关联的符号是username / repository：tag。 该标签是可选的，但建议使用，因为它是注册管理机构用于为Docker镜像提供版本的机制。 为该上下文提供存储库并标记有意义的名称，例如get-started：part2。这将图像放入启动存储库并将其标记为part2。 

现在，把它放在一起来标记图像。使用您的用户名，存储库和标签名称运行码头标签图像，以便将图像上传到您想要的目的地。该命令的语法是： 

```
docker tag image username/repository:tag
```

例如：

```
docker tag friendlyhello john/get-started:part2
```

运行[docker image ls](https://docs.docker.com/engine/reference/commandline/image_ls/) 以查看新标记的图像 

```
$ docker image ls

REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
friendlyhello            latest              d9e555c53008        3 minutes ago       195MB
john/get-started         part2               d9e555c53008        3 minutes ago       195MB
python                   2.7-slim            1c7128a655f6        5 days ago          183MB
...
```

#### 发布镜像

将您的标记镜像上传到存储库： 

```
docker push username/repository:tag
```

完成后，此上传的结果将公开发布。如果您登录到Docker Hub，则可以通过其pull命令在那里看到新镜像。 

#### 从远程存储库中提取并运行图像 

从现在起，您可以使用docker run并使用此命令在任何机器上运行您的应用程序： 

```
docker run -p 4000:80 username/repository:tag
```

如果图像在机器上本地不可用，则Docker将其从存储库中取出。 

```
$ docker run -p 4000:80 john/get-started:part2
Unable to find image 'john/get-started:part2' locally
part2: Pulling from john/get-started
10a267c67f42: Already exists
f68a39a6a5e4: Already exists
9beaffc0cf19: Already exists
3c1fe835fb6b: Already exists
4c9f1fa8fcb8: Already exists
ee7d8f576a14: Already exists
fbccdcced46e: Already exists
Digest: sha256:0601c866aab2adcc6498200efd0f754037e909e5fd42069adeff72d1e2439068
Status: Downloaded newer image for john/get-started:part2
 * Running on http://0.0.0.0:80/ (Press CTRL+C to quit)
```

无论`docker run` 在哪里执行， 它会将您的图像以及Python和requirements.txt中的所有依赖关系一起拉出，并运行您的代码。它们都在一个整洁的小包中一起旅行，并且您不需要在主机上安装任何Docker来运行它。 

### 本部分总结

这就是本部分所有内容。在下一节中，我们将学习如何通过在服务中运行此容器来扩展我们的应用程序。 

### 本部分命令总结

```
docker build -t friendlyhello .  		  # 使用此目录的Dockerfile创建镜像
docker run -p 4000:80 friendlyhello   # 运行“friendlyname”映射端口4000到80
docker run -d -p 4000:80 friendlyhello         	 # 同样的事情，但在分离模式
docker container ls                                	  # 列出所有运行的容器
docker container ls -a                  # 列出所有容器，即使那些未运行的容器
docker container stop <hash>                         # 优雅地停止指定的容器
docker container kill <hash>         				 # 强制关闭指定的容器
docker container rm <hash>                         # 从本机中移除指定的容器
docker container rm $(docker container ls -a -q)            # 删除所有容器
docker image ls -a                                 # 列出此机器上的所有镜像
docker image rm <image id>                           # 从本机删除指定的镜像
docker image rm $(docker image ls -a -q)               # 删除本机的所有镜像
docker login                                  # 使用Docker凭证登录此CLI会话
docker tag <image> username/repository:tag      # 标记<image>以上传到注册表
docker push username/repository:tag              # 将标记的镜像上传到注册表
docker run username/repository:tag                       # 从注册表运行映像
```

[原文链接](https://docs.docker.com/get-started/part2/)