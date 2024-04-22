
参考：[Docker and QEMU: A Powerful Combination for Accelerating Edge Computing Development and Optimizing Code Compilation](https://medium.com/@nullbyte.in/docker-and-qemu-a-powerful-combination-for-accelerating-edge-computing-development-and-optimizing-42da00259a02)

# 安装
[官方网站](https://docs.docker.com/desktop/install/ubuntu/)
![[Docker_安装.png]]

按照这两步去完成安装

去[github](https://github.com/nullbyte91/Docker_QEMU_system_emulation)上拉取容器

依次执行
![[Pasted image 20240328170153.png]]
（以下解释来自<font color="#ffff00">chatGPT3.5</font>）
1.<font color="#f79646">sudo apt-get install -y qemu-user-static</font>
这个命令安装了 QEMU 用户模式静态编译器。QEMU 用户模式静态编译器允许在一个架构上运行另一个架构的二进制文件。这在构建和运行多架构 Docker 镜像时非常有用，因为它允许在 x86 主机上构建和运行 ARM 或其他架构的 Docker 镜像。

通过运行上述命令，你已经在系统中安装了 QEMU 用户模式静态编译器，这样在后续构建多架构 Docker 镜像时就可以利用它了

2.<font color="#f79646">docker build --rm --tag i386:v1 -f arm64v8.Dockerfile .</font>
这个命令尝试使用一个名为 `arm64v8.Dockerfile` 的 Dockerfile 文件来构建一个 Docker 镜像，并将其标记为 `i386:v1`。这似乎是一个混淆的命令，因为 Dockerfile 的命名 (`arm64v8.Dockerfile`) 暗示了它是为 ARM64 架构设计的，但是标记 (`i386:v1`) 暗示了它是为 x86 架构设计的。

3.<font color="#f79646">docker build --rm --tag i386:v1 -f arm64v8_tf.Dockerfile .</font>
这个命令似乎再次尝试使用一个名为 `arm64v8_tf.Dockerfile` 的 Dockerfile 文件来构建一个 Docker 镜像，并将其标记为 `i386:v1`。与之前的命令类似，这也是一个混淆的命令，因为 Dockerfile 的命名 (`arm64v8_tf.Dockerfile`) 暗示了它是为 ARM64 架构设计的，但是[[Docker标记]] (`i386:v1`) 暗示了它是为 x86 架构设计的。

4.<font color="#f79646">
docker build --rm --tag i386:v1 -f arm64v8_armnn.Dockerfile .
 .</font>
在这个命令中，使用了 `docker build` 命令来构建一个 Docker 镜像，并且给这个镜像指定了标记为 `i386:v1`。

具体解释如下：

- `--rm`：表示构建完成后删除临时容器。这个选项可以确保在构建完成后不会留下任何无用的临时容器。
- `--tag i386:v1`：指定了构建的镜像的标记为 `i386:v1`。这个标记将用于引用和使用这个镜像。
- `-f arm64v8_armnn.Dockerfile`：指定了要使用的 Dockerfile 文件为 `arm64v8_armnn.Dockerfile`。Docker 将会根据这个文件中定义的指令来构建镜像。
- `.`：表示 Dockerfile 文件所在的路径。在这个命令中，`.` 表示当前目录，Docker 将在当前目录下查找并读取 `arm64v8_armnn.Dockerfile` 文件进行构建。

总的来说，这个命令的作用是使用 `arm64v8_armnn.Dockerfile` 文件构建一个 Docker 镜像，并将其标记为 `i386:v1`。

# <font color="#c0504d"> PS:其实以上内容均未成功</font>
看这两篇文章
[Setting Up ARM Emulation on x86 ](https://www.stereolabs.com/docs/docker/building-arm-container-on-x86)
[在x86机器上的Docker运行arm64容器](https://blog.csdn.net/weixin_41967328/article/details/132489636)

这个命令查看镜像
docker images

这个命令最终进入
docker run -it arm64v8/ubuntu:20.04 /bin/bash

这个命令复制
sudo docker cp aaaaaaaa f741e30a7d35:/home

这个命令给容器命名启动
docker run --name my_container -it ubuntu:20.04 /bin/bash

这个命令进入已经命名的容器
sudo docker exec -it my bash 