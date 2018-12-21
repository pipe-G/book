#Docker
Docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器。开发者在笔记本上编译测试通过的容器可以批量地在生产环境中部署，包括VMs（虚拟机）、bare metal、OpenStack 集群和其他的基础应用平台。 

Docker通常用于如下场景：
>1.web应用的自动化打包和发布；
>2.自动化测试和持续集成、发布；
>3.在服务型环境中部署和调整数据库或其他的后台应用；
>4.从头编译或者扩展现有的OpenShift或Cloud Foundry平台来搭建自己的PaaS环境。

###Docker命令

#### 容器
1.查看容器信息
docker container ls
docker ps
2.停止容器
docker container stop webserver
3.使用“all”标志（--all或-a）查看已停止的容器
docker ps -a
docker container ls -a
4.删除容器
docker container rm webserver
docker rm
删除所有
docker rm (docker ps -a -q)

5.启动、停止、重启某个docker 容器
docker start 容器名、id
docker stop  容器名、id
docker restart 容器名、id
6.查看指定容器的日志记录
docker logs -f 容器名、id


#### 镜像

1.查看镜像
docker image ls
docker images
2.删除镜像
docker image rm nginx

###镜像
分层存储
###Docker容器
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等
容器存储层，以镜像为基础层，在其上创建存储层。
容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录
###仓库
一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。
通过 **<仓库名>:<标签>** 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

* 获取镜像:
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]

* 列出镜像:
docker image ls
REPOSITORY 、TAG、IMAGE ID 、CREATED、SIZE

仓库名、标签、镜像 ID、
创建时间 、所占用的空间

* 查看镜像、容器、数据卷所占用的空间
docker system df
* 无标签镜像也被称为 虚悬镜像(dangling image) 
 docker image ls -f dangling=true
* 中间层镜像 
docker image ls -a
* 可以根据仓库名，或者仓库名和标签，加在ls 后
* 支持强大的过滤器参数 --filter
mongo:3.2 之后建立的镜像：
docker image ls -f since=mongo:3.2
查看某个位置之前的镜像也可以，只需要把 since 换成 before 即可
* 包含镜像ID和仓库名
docker image ls --format "{{.ID}}: {{.Repository}}"
* 删除本地镜像
docker image rm
* 重命名镜像
docker tag IMAGEID(镜像id) REPOSITORY:TAG（仓库：标签）
镜像摘要
docker image ls --digests
* 删除所有仓库名为 redis 的镜像 
-q 列出ID
docker image rm $(docker image ls -q redis)

###使用 Dockerfile 定制镜像
Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建.
FROM 和 RUN

####FROM 指定基础镜像
Docker 还存在一个特殊的镜像，名为 scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像.
如果你以 scratch 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

####RUN 执行命令
Dockerfile 中每一个指令都会建立一层，RUN 也不例外，可以使用这种形式，避免多层。
RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
###DockerFile

1. COPY复制文件
COPY [--chown=<user>:<group>] <源路径>... <目标路径>
COPY [--chown=<user>:<group>] ["<源路径1>",... "<目标路径>"]
COPY package.json /usr/src/app/
--chown=<user>:<group> 选项来改变文件的所属用户及所属组
COPY --chown=55:mygroup files* /mydir/
2. ADD 更高级的复制文件
ADD 指令和 COPY 的格式和性质基本一致。但是在 COPY 基础上增加了一些功能
自动解压缩的功能
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /

### Compose
Compose是一个用于定义和运行多个Docker容器的编排工具。可以一条命令启动多个容器。主要是解决了容器与容器之间如何管理编排的问题。


按官网的说明，使用Compose基本上也就分成三步：

利用Dockerfile定义运行环境
使用docker-compose.yml定义组成应用的各服务
运行docker-compose up启动应用


docker-compose up 启动docker compose中定义的服务，参数 -d 以守护进程的方式运行compos，注意服务间是交替运行的，Ctrl + c停止compose 运行 
-f 指定文件名，运行之后，web端口是根据nginx指定的

docker-compose ps 查看本地compose定义的正在运行的所有服务，类似于docker ps

docker-compose logs 查看日志，这个命令会追踪日志文件类似于docker logs -f container 或者tail -f file 一样

docker-compose stop 停止正在运行的服务，如果服务并没有停止，则可以通过docker kill 强制停止服务

docker-compose start 重启服务，与docker start 类似

docker-compose rm 删除compose 服务，类似于docker rm
