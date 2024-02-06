#linux 

# 简介

Docker 是一种轻量级的虚拟化技术，它允许在同一台物理机上运行多个隔离的容器，每个容器都有自己的文件系统、网络和资源隔离。Docker 由两部分组成：Docker 镜像和 Docker 容器。

Docker 镜像是一个静态文件，其中包含了一个完整的文件系统，包括应用程序、库、依赖项和配置文件等。Docker 容器则是由 Docker 镜像启动的一个运行实例，它可以被启动、停止、删除和重启等。Docker 容器是轻量级的，启动和停止速度非常快。

Docker 镜像需要存储在 Docker 仓库中，也称为 Docker Registry。Docker Registry 是一个集中的存储库，可以存储、管理和分享 Docker 镜像。Docker 官方提供了 Docker Hub 作为默认的 Docker Registry，用户可以在其中查找、下载和上传 Docker 镜像。此外，用户也可以搭建自己的 Docker Registry。

总之，Docker 镜像是一个静态的文件，Docker 容器是由 Docker 镜像启动的一个运行实例，Docker 仓库是存储、管理和分享 Docker 镜像的集中存储库。

## Docker Desktop

Docker Desktop 为一款图形界面工具， 降低容器打包， 使用， 配置难度。

## Docker engine

Docker 采用 c/s 架构， `dockerd` 为服务端， 运行容器， `docker` 为  cli 客户端， 使用 docker 提供的 api 同 dockerd 通讯。Docker engine  为docker 服务端和客户端集合。如果你只想运行 Doker 容器， 只安装 Docker engine 即可。

Docker engine 可以管理容器方方面面：

- storage
- networking
- logs
- prune
- confing daemon
- rootless

## Docker build

Docker build 是 docker engine 核心功能， 用以打包自己的 docker 镜像。使用 `docker build`  或者 `docker buildx` 可打包自己的镜像， 自 `18.09` 版本， 使用更新的 `buildkit`。

## Docker Compose

Docker compose 是一个工具， 使用 yaml 来配置， 可以一键运行多个容器。

## Docker Hub

由 docker 提供的的镜像分享、搜索服务， 为世面上最大的 docker 镜像仓库。

## Docker Scout

是一套工具， 用于分析镜像， 对镜像组成和安全有详尽的透视， 并生成报告。


# 安装

下面的教程会安装以下模块：

- docker-ce 
- docker-ce-cli 
- containerd.io 
- docker-buildx-plugin 
- docker-compose-plugin

可以运行， 打包， 使用 compose 等, 如果后续重新安装， 则需要重新实施准备工作， 使用 docker apt 仓库， 默认仓库没有 docker-xxxx-plugin。安装和升级都需要卸载原来， 可以参考[[#卸载]]

## 从仓库

[参看](https://docs.docker.com/engine/install/ubuntu/)

### 准备工作

1. 更新仓库， 安装 https 用到的依赖

```shell
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg
```

2. 添加 docker 官方 GPG key

```shell
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

3. 安装

```shell
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 安装 docker engine

1. 更新 apt 目录

```shell
sudo apt-get update
```

2. 安装 docker engine, container, compose

```shell
#最新
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

#指定版本
# List the available versions:
apt-cache madison docker-ce | awk '{ print $3 }'

5:20.10.16~3-0~ubuntu-jammy
5:20.10.15~3-0~ubuntu-jammy
5:20.10.14~3-0~ubuntu-jammy
5:20.10.13~3-0~ubuntu-jammy

VERSION_STRING=5:20.10.13~3-0~ubuntu-jammy
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

3. 运行 hello word

```shell
sudo docker run hello-world
```

### 用便捷安装脚本

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

## 从源码

#todo

## 从二进制包

#todo

# 更新

## 从仓库安装后更新

1. 访问 https://download.docker.com/linux/ubuntu/dists/
2. 选择适合你 ubuntu 的版本
3. 选择程序架构， 在 pool/stable 文件夹中
4. 下载 deb 文件， 包括 Docker Engine, CLI, containerd, and Docker Compose

	-   `containerd.io_<version>_<arch>.deb`
	-   `docker-ce_<version>_<arch>.deb`
	-   `docker-ce-cli_<version>_<arch>.deb`
	-   `docker-buildx-plugin_<version>_<arch>.deb`
	-   `docker-compose-plugin_<version>_<arch>.deb`

5. 安装下载的 deb

```shell
sudo dpkg -i ./containerd.io_<version>_<arch>.deb \
  ./docker-ce_<version>_<arch>.deb \
  ./docker-ce-cli_<version>_<arch>.deb \
  ./docker-buildx-plugin_<version>_<arch>.deb \
  ./docker-compose-plugin_<version>_<arch>.deb
```

6. 验证安装

```shell
sudo service docker start
sudo docker run hello-world
```

## 从快捷脚本安装后更新

1. 卸载安装的包，  Docker Engine, CLI, containerd, and Docker Compose

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

2. 删除文件

```shell
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

3. 重新用快捷脚本安装

# 卸载

新旧版本的组件大为不同， 新旧版本需要卸载的组件如下

1. **新版本**
	- docker-ce 
	- docker-ce-cli 
	- containerd.io 
	- docker-buildx-plugin 
	- docker-compose-plugin 
	- docker-ce-rootless-extras
1. **旧版本**
	- docker 
	- docker-engine 
	- docker.io 
	- containerd 
	- runc
  
要彻底卸载 Docker，需要执行以下步骤：

1.  停止 Docker 服务：


```shell
sudo systemctl stop docker
```

2.  删除 Docker 软件包及其依赖项：


```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

3.  删除 Docker 数据目录：


```shell
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

4.  删除 Docker 用户组：


```shell
sudo groupdel docker
```

注意：以上操作会删除 Docker 的所有数据，包括容器、镜像等。请谨慎操作。

# 加载镜像

可以从 docker hub 获取， 也可以导入本地镜像， 导入完毕后， 可以使用 `docker images` 查看镜像的信息。

## 从 docker hub 加载

到 docker hub 上搜索， 复制 pull 命令。

```shell
docker pull repo:tag
#版本由 tag 指定
```

## 从本地加载

docker load 是 Docker 命令之一，用于将本地文件系统中的 Docker 镜像加载到 Docker 引擎中。它的用法如下：

Copy
docker load < 文件名
其中，< 文件名 表示要加载的 Docker 镜像文件的路径和文件名。

使用 docker load 命令可以在不需要网络连接的情况下将 Docker 镜像导入到本地 Docker 引擎中。它的作用类似于 docker pull 命令，不同之处在于 docker pull 是从 Docker Hub 或其他远程镜像仓库中下载镜像到本地，而 docker load 是将本地已有的 Docker 镜像导入到 Docker 引擎中。

# 修改镜像

docker 允许修改已经存在的镜像， 包括删除某些文件， 添加某些文件等。

## 从镜像中删除文件

要从 Docker 镜像中删除文件，需要先创建一个新的 Dockerfile 文件，然后使用 Docker build 命令重新构建镜像。以下是具体步骤：

1.  在本地创建一个空目录，用于存放 Dockerfile 文件和需要删除的文件。
    
2.  在该目录下创建一个新的 Dockerfile 文件，输入以下内容：
    
    Copy
    
    ```
    FROM <原始镜像>
    
    RUN rm <需要删除的文件路径>
    ```
    
    其中，`<原始镜像>` 是你要删除文件的镜像名称，`<需要删除的文件路径>` 是你要删除的文件的路径。
    
3.  使用 Docker build 命令重新构建镜像：
    
    ```shell
    docker build -t <新镜像名称> .
    ```
    
    其中，`<新镜像名称>` 是你要构建的新镜像名称，`.` 表示 Dockerfile 文件所在目录。
    
4.  构建完成后，可以使用 Docker images 命令查看新的镜像是否已经生成。
    
5.  如果需要使用新的镜像，可以使用 Docker run 命令启动容器。
    

注意：如果你要删除的文件位于镜像的根目录下，可以使用以下命令：

```shell
RUN rm /<需要删除的文件名称>
```

其中，`<需要删除的文件名称>` 是你要删除的文件名称。

## 导入文件到镜像

**方案一**

`docker import` 是将本地文件或 URL 的内容导入到 Docker 镜像的命令。

该命令的语法格式为：


```
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```

其中，`file|URL|-` 表示要导入的本地文件、URL 或标准输入流。`REPOSITORY[:TAG]` 表示要创建的镜像的名称及标签。

常用的选项包括：

-   `--change`：指定 Dockerfile 中的 `CMD`、`ENTRYPOINT` 或 `ENV` 指令。
-   `--message`：设置导入镜像的描述信息。
-   `--platform`：指定镜像的平台架构。
-   `--quiet`：静默模式，不输出导入的镜像 ID。

例如，将本地文件 `myimage.tar` 导入为名为 `myrepo/myimage:latest` 的 Docker 镜像，可以使用以下命令：


```
docker import myimage.tar myrepo/myimage:latest
```

注意，使用 `docker import` 命令导入的镜像不会包含任何元数据，如 CMD、ENTRYPOINT、ENV 等信息。如果需要包含这些信息，建议使用 `docker build` 命令构建镜像。

**方案二**

要向已经存在的 Docker 镜像中添加文件，可以使用 Docker commit 命令。这个命令可以将容器的修改保存为一个新的镜像。

以下是具体步骤：

1.  启动一个容器：
   
    ```shell
    docker run -it <镜像名称> /bin/bash
    ```
  
   其中，`<镜像名称>` 是你要添加文件的镜像名称。
  
2.  在容器中添加文件，例如：
  
    ```shell 
    touch /tmp/newfile.txt
    ```
  
这将在容器中创建一个新的文件 `/tmp/newfile.txt`。
 
3.  退出容器：

   
    ```shell
    exit
    ```
   
4.  使用 Docker commit 命令将容器的修改保存为一个新的镜像：
 
  
    ```shell
    docker commit <容器ID> <新镜像名称>
    ```
  
 其中，`<容器ID>` 是你要提交的容器的 ID，可以使用 `docker ps -a` 命令查看容器的 ID；`<新镜像名称>` 是你要保存的新镜像名称。
  
5.  构建完成后，可以使用 Docker images 命令查看新的镜像是否已经生成。


相比之下，Docker import 命令可以将本地文件系统上的文件或目录导入为一个新的镜像。与 Docker commit 命令不同，Docker import 命令不需要先启动容器。但是，Docker import 命令会忽略所有容器的历史记录和元数据。因此，如果你需要保留容器的历史记录和元数据，应该使用 Docker commit 命令。

# 删除镜像

docker rm是Docker命令行工具中的一个命令，用于删除一个或多个已停止的容器。其基本用法为：


```shell
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```

其中OPTIONS是可选的参数，CONTAINER表示要删除的容器名称或ID。例如，要删除ID为1234和5678的两个容器，可以执行以下命令：


```shell
docker rm 1234 5678
```

此命令将永久删除这两个容器，且无法恢复。如果要删除正在运行的容器，可以添加-f选项来强制删除。例如：


```shell
docker rm -f mycontainer
```

此命令将强制停止并删除名为mycontainer的容器。

# 运行镜像

运行之前必须加载相应镜像到 docker 引擎中, 可以使用 `docker images` 来验证是否加载成功,  还需要完成相应的配置。可使用 `docker run` 或 `docker compose` 来运行。

## docker run 方式

### 配置

## docker compose 方式

### 配置
