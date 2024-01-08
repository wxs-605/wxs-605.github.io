# docker基本命令使用


  在kubernets项目中多次使用到了docker的相关指令，特在此记录。

# Docker 基本命令的使用方法

## 镜像的拉取、上传和删除

- 搜索镜像

```shell
docker search [imageName]:[tag]
```
- 拉取镜像

```shell
docker pull [imageName]:[tag]
```

- 上传镜像

```shell
docker push [imagesName]:[tag]
```

- 删除镜像

```shell
docker rmi [镜像名]:[tag]
或
docker rmi [镜像ID]
```

如果删除不成功，可以在上面的删除命令后面加上参数强制删除，如：

```shell
docker rmi [imageName]:[tag] --force
```

上述命令可以强制删除镜像，即使这个镜像正在被使用。

注意： 强制删除镜像的命令对于拥有子镜像的镜像将不会成功，除非先删除他的子镜像。

---

## 镜像打包与导入

- 打包镜像

```shell
docker save -o [打包后的镜像名称].tar [imagesName]:[tag]
```

- 通过tar包导入镜像

```shell
docker load<[打包的镜像名].tar
```
__注意__ 其中的 < 符号前后连接没有空格。

## docker制作镜像
```shell
docker build -t [你制作的镜像名]:[tag] .
```

其中的符号 . 表示Dockerfile文件在当前文件夹下，并使用其进行制作镜像。

---

## Dockerfile 简介

**什么是Dockerfile?**

Dockerfile是一个用来构建镜像的文本文件，里面包含了构建镜像所需的指令。

### Dockerfile指令介绍

- FROM

所有Dockerfile指令都必须从FROM命令开始。FROM命令会指定镜像基于哪个基础镜像构建，接下来的命令也会给予这个基础镜像，FROM命令可以多

次使用，表示会创建多个镜像。

```
FROM <images name>
```

- MAINTAINER

MAINTAINER命令用于指定该镜像的作者。

```
MAINTAINER <author name>
```

- RUN 

RUN命令在shell或者exec的环境下执行的命令。RUN指令会在新创建的镜像上添加新的层面，接下来提交的结果用在Dockerfile的下一条指令中。语法如下：

```
RUN <command>
RUN apt-get update //这种形式使用/bin/sh环境中执行的命令
RUN ["apt-get", "update"] //这种形式直接使用系统调用exec来执行

多个命令可以用符号 && 连接执行，如：
RUN apt-get update && apt-get install vim
```

- ADD
ADD是文件复制指令。它有两个参数<source>和<destination>。destination是容器内的路径。source可以是URL或者是启动配置上下稳重的一个文件。语法如下：

```
ADD <src> <destination>
```

如果源文件是主机上的zip或tar形式的压缩文件，Docker会先解压缩，然后将文件添加到镜像指定的文件。如果是URL指定的网络压缩文件则不会解压。

- CMD
CMD指令提供了容器启动时默认执行的命令。**Dockerfile只允许使用一次CMD指令。** 使用多个CMD指令会抵消之前所有的指令，只有最后一个指令生效。CMD有三种形式：

```
CMD ["executable", "paraml", "param2"]
CMD ["param1", "param2"]
CMD command param1 param2
```

- EXPOSE

EXPOSE指定容器在运行时监听的端口。语法如下：

```
EXPOSE <port>; //多个端口用空格隔开
EXPOSE 80 8080 9000
```

运行容器时通过参数-P(大写)即可在EXPOSE里所指定的端口映射到主机上另外的随机端口，其他容器或主机就可以通过映射后的端口与此容器通信。同时，我们也可以通过-p(小写)参数将Dockerfile中EXPOSE没有列出的端口设置成公开的。

- ENTRYPOINT

ENTRYPOINT：配置给容器一个可执行的命令。这意味着每次使用镜像创建容器时一个特定的应用程序可以被设置为默认程序。同时也意味着该镜像每次被调用时仅能运行指定的应用。类似CMD，Docker只允许一个ENTRYPOINT,多个ENTRYPOINT会抵消之前所有的ENTRYPOINT命令，只执行最后一句。其语法如下： 

```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```

- WORKDIR

WORKDIR指定RUN、CMD和ENTRYPOINT命令的工作目录。语法如下：

```
WORKDIR /path/to/workdir
```

- ENV

ENV指令用于设置环境变量。它们使用键值对，增加运行程序的灵活性，语法如下：

```
ENV <key> <value>
```

以上是我本人在kubernetes项目中常用的指令，特在此记录。

另外，Dockerfile文件的理解是站在巨人的肩膀上，[连接在此](https://www.yisu.com/zixun/558855.html)

