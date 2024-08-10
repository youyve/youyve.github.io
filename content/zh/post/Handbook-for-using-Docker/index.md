---
title: 🎉 Docker使用手账
summary: Take full control of your personal brand and privacy by migrating away from the big tech platforms!
date: 2023-10-27

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**github**](https://github.com/ZequnZ/python-x-docker-handbook/blob/master/asset/banner.png)'

authors:
  - youlz
  - Ted

tags:
  - Docker
  - Handbook
  - Markdown
---

Welcome 👋

{{< toc mobile_only=true is_open=true >}}

## 将用户添加到docker组

要将用户添加到 `docker` 组并使其立即生效，可以按照以下步骤操作：

1. 使用 `usermod` 命令将用户添加到 `docker` 组。将 `<your_user>` 替换为要添加的用户名：

   ```bash
   sudo usermod -aG docker <your_user>
   ```

   这个命令会将 `<your_user>` 用户添加到 `docker` 组中。`-aG` 选项中的 `-a` 表示追加，`-G` 表示指定组。

   应用改变而不用注销

2. 为了让用户组的改变立即生效，而不用注销和重新登录，可以使用 `newgrp` 命令或者切换到另一个会话。

   使用 `newgrp` 命令切换到 `docker` 组：

   ```bash
   newgrp docker
   ```

   这个命令会启动一个新的 shell 会话，并且在这个会话中，用户是 `docker` 组的成员。

   或者，如果您正在使用 `su` 命令切换用户，可以重新切换回您的用户：

   ```bash
   su - <your_user>
   ```

3. 测试用户是否正确添加到 `docker` 组

   可以使用 `id` 命令来确认用户是否已经是 `docker` 组的成员：

   ```bash
   id -nG <your_user>
   ```

   如果一切正常，应该能在输出的用户组列表中看到 `docker`。

4. 测试 Docker 命令

   最后，测试一下不使用 `sudo` 运行 Docker 命令：

   ```bash
   docker run hello-world
   ```

   如果没有错误，并且容器正常运行，这表明用户已经成功加入到 `docker` 组，并且更改已生效。

## Docker 镜像的常见操作

### 构建 Docker 镜像 

- **从 Dockerfile 构建镜像**：   

  需要与 Dockerfile 在同一个目录下

  ```bash  
  docker build -t <镜像名>:<标签> <Dockerfile所在目录> 
  ```

  这里 `-t` 用于指定要构建的镜像的名称和标签，通常包含 `<username>/<repository>:<tag>` 的格式。例如 `myusername/myapp:v1.0`，其中标签常作为版本号使用。 

  可以不与 Dockerfile 在同一个目录下，指定 Dockerfile 的路径即可

  ```bash
  docker build -t <tag_of_your_image> -f /path/to/your/Dockerfile
  ```

- **docker小常识**：

  只有在容器在被创建时才能使用 -v 参数挂载本地数据，容器创建之后就不能挂在本地数据了，只能通过 docker cp 将本地数据拷贝到容器中

  Docker 在 Linux 系统上使用 `/var/lib/docker/` 目录作为存放镜像、容器、卷和网络的默认位置。镜像实际上存储在 `/var/lib/docker/image/` 的子目录中，而具体的存储驱动子目录（如 `aufs`、`overlay2` 等）则包含了镜像的层。

### 查看镜像

- **列出本地所有镜像**：   

  ```bash  
  docker images 
  ```

- **查看某个镜像的历史**：   

  ```bash  
  docker history <镜像名>:<标签>
  ```

### 使用镜像

- **运行容器实例**：   

  从镜像启动一个新容器：   

  ```bash
  docker run -d --name <容器名称> <镜像名>:<标签>
  ```

  `-d` 表示后台运行容器，`--name` 用于指定容器的名称。 

- **在容器中执行命令**：   

  使用 `docker exec` 在运行的容器中执行命令：   

  ```bash  
  docker exec -it <容器名称> <命令>
  ```

### 管理镜像

- **标记镜像**：   

  为镜像添加一个新的标签：   

  ```bash 
  docker tag <原镜像名>:<原标签> <新镜像名>:<新标签>
  ```

- **删除镜像**：   

  删除一个本地镜像：   

  ```bash 
  docker rmi <镜像名>:<标签>
  ```

### 推送和拉取镜像 

- **拉取镜像**：   

  从 Docker Hub 或其他注册中心拉取一个镜像：   

  ```bash  docker pull <镜像名>:<标签>  ``` 

- **推送镜像**：   

  将本地镜像推送到 Docker Hub 或其他注册中心（首先需要登录）：   

  ```bash  
  docker push <镜像名>:<标签>
  ```

### 保存和加载镜像

- **保存镜像到一个 tar 归档**：  

  ```bash  
  docker save -o <文件名>.tar <镜像名>:<标签>
  ```

- **从 tar 归档加载镜像**：   

  ```bash  
  docker load -i <文件名>.tar
  ```

### 删除操作

- **删除容器镜像**：

  ```bash
  docker rmi <镜像ID或名称>
  ```

  

## Docker 容器的常见操作

启动 Docker 容器时，可以通过不同的命令和参数来控制其行为。这些操作允许你配置容器的网络、存储、资源限制等方面。以下是一些启动容器时的常见操作： 

###  基础启动命令

 **启动一个新容器**：   

```bash  docker run [OPTIONS] IMAGE [COMMAND] [ARG...]  ```   

其中，`[OPTIONS]` 是启动容器时可以使用的各种选项，`IMAGE` 是你要启动的容器镜像，`[COMMAND]` 和 `[ARG...]` 是可选的，指定容器启动后要运行的命令及其参数。 

### 常见的启动选项 

- **后台运行** (`-d` 或 `--detach`)：在后台运行容器。  

  ```bash
  docker run -d IMAGE
  ```

- **命名容器** (`--name`)：为容器指定一个名称。   

  ```bash
  docker run --name my_container IMAGE 
  ```

- **端口映射** (`-p` 或 `--publish`)：将容器内的端口映射到宿主机的端口。   

  ```bash
  docker run -p host_port:container_port IMAGE 
  ```

- **环境变量** (`-e` 或 `--env`)：设置容器内的环境变量。   

  ```bash
  docker run -e "KEY=value" IMAGE 
  ```

- **挂载卷** (`-v` 或 `--volume`)：将宿主机的目录或文件挂载到容器内。   

  ```bash
  docker run -v host_path:container_path IMAGE 
  ```

- **交互式和TTY** (`-i` 和 `-t`)：以交互式方式运行容器，并分配一个伪终端。   

  ```bash  
  docker run -it IMAGE /bin/bash 
  ```

- **限制资源**：可以限制 CPU (`--cpus`)、内存 (`--memory`) 等资源。   

  ```bash  
  docker run --cpus=".5" --memory="100m" IMAGE  
  ```

- **网络配置** (`--network`)：指定容器的网络模式。   

  ```bash  
  docker run --network=host IMAGE 
  ```

- **运行健康检查** (`--health-cmd`)：指定容器的健康检查命令。   

  ```bash  
  docker run --health-cmd="curl -f http://localhost/ || exit 1" IMAGE 
  ```

### 其他高级选项 

- **自动重启** (`--restart`)：设置容器的重启策略。   

  ```bash  
  docker run --restart=always IMAGE  
  ```

- **只读文件系统** (`--read-only`)：以只读模式运行容器的文件系统。   

  ```bash  
  docker run --read-only IMAGE 
  ```

- **使用 GPU** (`--gpus`)：允许容器访问 GPU 资源。   

  ```bash  
  docker run --gpus all IMAGE 
  ```

  这些仅仅是启动 Docker 容器时可以使用的一部分选项。具体使用哪些选项，取决于你的具体需求和你要运行的容器镜像。可以通过 `docker run --help` 命令查看更多的选项。

- **更多参数设置**：

  ```bash
  docker run --gpus all --ipc=host --network=host --rm --name <容器ID或名称> -it <镜像ID或名称> bash
  ```

- **挂载多个本地文件或文件夹**：

  ```bash
  docker run -v /path/to/local/folder1:/path/to/container/folder1 \
             -v /path/to/local/folder2:/path/to/container/folder2 \
             my-image
             
  docker run --mount type=bind,source=/path/to/local/folder1,target=/path/to/container/folder1 \
             --mount type=bind,source=/path/to/local/folder2,target=/path/to/container/folder2 \
             my-image
  ```

### 对docker容器的一些操作

### 查看容器

- **列出正在运行的容器**：

  ```bash
  docker ps
  ```

- **列出所有容器（包括未运行的）**：

  ```bash  
  docker ps -a
  ```

### 启动和停止容器

- **启动容器**：

  ```bash
  docker start <容器ID或名称>
  ```

- 在启动容器的同时接入其命令行界面，可以使用attach命令：

  ```bash
  docker start -a <容器ID或名称>
  ```

- **停止正在运行的容器**：

  ```bash
  docker stop <容器ID或名称>
  ```

### 进入容器

- 使用 exec 命令在运行的容器中创建新的容器会话：

  ```bash
  docker exec -it <容器ID或名称> /bin/bash
  ```

- 使用 attach 命令连接到正在运行的容器会话：

  ```bash
  docker attach <容器ID或名称>
  ```

  注意：使用 `attach` 命令时，会进入容器的当前运行会话。使用 `Ctrl + C` 会停止容器。要离开而不停止容器，需要使用 `Ctrl + P`，`Ctrl + Q` 的组合键。

### 重启容器

- **重启容器**：

  ```bash  
  docker restart <容器ID或名称>
  ```

### 删除容器

- **删除容器（容器必须处于停止状态）**：

  ```bash
  docker rm <容器ID或名称>
  ```

- **强制删除正在运行的容器**：

  ```bash
  docker rm -f <容器ID或名称>
  ```

- 不想删除现有的容器，并**重命名**：

  ```bash
  docker rename <容器ID或名称> new_name
  ```

- **删除对应image的所有容器**：

  ```bash
  docker ps -a | grep mmengine | awk '{print $1}' | xargs docker rm -f
  ```

### 查看容器日志

- **查看容器的输出日志**：

  ```bash
  docker logs <容器ID或名称>
  ```

### 复制文件和目录

- **从容器复制文件/目录到主机**：

  ```bash
  docker cp <容器ID或名称>:/path/in/container /path/on/host
  ```

- **从主机复制文件/目录到容器**：   

  ```bash 
  docker cp /path/on/host <容器ID或名称>:/path/in/container
  ```

### 查看容器信息

- **查看容器的详细信息**：

  ```bash
  docker inspect <容器ID或名称>
  ```

- **查看容器的资源使用情况**：

  ```bash
  docker stats <容器ID或名称>
  ```

### 暂停和恢复容器

- **暂停容器**：

  ```bash
  docker puase <容器ID或名称>
  ```

- **恢复容器**：

  ```bash
  docker unpuase <容器ID或名称>
  ```

  


## Docker镜像管理

- **提交一个容器为新的镜像**

  `docker commit` 命令的基本用法

  ```bash
  docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  ```

  其中：

  `CONTAINER` 是你想要提交的容器的名称或 ID。

  `REPOSITORY` 是新镜像的仓库名称。

  `TAG` 是新镜像的标签，**默认为 `latest`**。

  例：

  ```bash
  docker commit qwen1.5 new_image
  
  docker commit qwen1.5 new_image:v1.0
  ```



## Docker镜像在设备间的传输

- **在其他设备上使用 Docker 镜像(docker save)**

  1. **使用 `docker save` 命令来将镜像保存为一个 tar 归档文件**：

     ```bash
     docker save -o <path for generated tar file> <image name>
     ```

     如：

     ```bash
     docker save -o myimage.tar myimage:latest
     ```

  2. **将 tar 文件传输到其他设备**：

     可以使用 scp 进行传输

     传输过程可能会存在文件损坏，需要检查 sha256：

     Linux：`sha256sum filename`

     Mac：`shasum -a 256 filename`

     Win：`Get-FileHash -Path "\path\qwen.tar" -Algorithm SHA256`

  3. **在目标设备上，使用 `docker load` 命令从 tar 文件中加载镜像**：

    ```bash
  docker load -i <path to image tar file>
    ```

  4. **运行镜像**：

    ```bash
  docker run -d <image name>
    ```

    

- **在其他设备上使用 Docker 镜像(dockerhub)**

  1. **登录到 Docker Hub**：

    ```bash
  docker login
    ```

    **登出**：

    ```bash
  docker logout
    ```

  2. **标记本地镜像**：

     使用 docker tag 命令为你的镜像创建一个标签，这个标签应该包含你的 Docker Hub 用户名、仓库名和镜像的版本标签。

     ```bash
     docker tag <local-image>:<tag> <docker-hub-username>/<repository>:<tag>
     ```

     例如：

     ```bash
     docker tag myimage:latest myusername/myimage:latest
     ```

  3. **推送镜像到 Docker Hub**：

     ```bash
     docker push <docker-hub-username>/<repository>:<tag>
     ```

  4. **在其他设备上拉取镜像**：

     ```bash
     docker pull <docker-hub-username>/<repository>:<tag>
     ```

  5. **运行镜像**：

     ```bash
     docker run -d <docker-hub-username>/<repository>:<tag>
     ```



## BUGS及其解决方法

- 报错：**docker: Error response from daemon: could not select device driver "" with capabilities: [[gpu]].**

  解决：

  **出现这个问题，通常是因为 NVIDIA Docker 支持没有正确安装或配置。**

  安装 NVIDIA Docker 支持:

  对于支持的 Linux 发行版，首先按照以下步骤安装

  添加 NVIDIA 容器库：

  ```bash
  distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
  curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
  curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
  ```

  然后，更新软件包列表并安装 `nvidia-docker2`：

  ```bash
  sudo apt-get update
  sudo apt-get install -y nvidia-docker2
  ```

  接下来，重启 Docker 守护进程以应用更改：

  ```bash
  sudo systemctl restart docker
  ```

  完成
