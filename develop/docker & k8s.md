## Docker

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20201223103631418.png" alt="image-20201223103631418" style="zoom:50%;" />

### 定义：

Docker是一个开源的***应用容器引擎***，开发者可以打包他们的应用及依赖到一个可移植的容器中，发布到流行的Linux机器上，也可实现虚拟化。

### 概念：

1. repository：用于保存image
2. image：镜像，用于创建容器的模版（建议用官方hub上的）
3. container：实际的容器运行实例
4. host：用于执行daemon进程和容器
5. Dockerfile：是对image的定制说明
6. docker-compose.yml：是对多个service的集成定制说明

### 命令：

1. docker ps：列出所有containers
2. docker build：为image创建一个container
3. docker run：启动运行一个containers
4. docker stop：停止1/more正在运行的containers
5. docker-compose up -d [service名]：为指定的service，build并且run一个container
6. docker exec -it [service Id] bash：连接进入某个service的容器中



### 注意⚠️

1. docker-compose中的port环境变量：用于暴露端口信息到宿主机，“容器外port：容器内port”
2. image最好从官方hub中找，并且搞清楚其支持的环境变量
3. Docker-compose模版教程：https://yeasy.gitbook.io/docker_practice/compose/compose_file



## K8S

### 定义：

k8s是一个开源的***容器集群管理系统***，可以实现容器集群的自动化部署、自动扩缩容、维护等功能

