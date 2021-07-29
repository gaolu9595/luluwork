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
7. docker rm：删除1/more已经停止运行的containers



### 注意⚠️

1. docker-compose中的port环境变量：用于暴露端口信息到宿主机，“容器外port：容器内port”
2. image最好从官方hub中找，并且搞清楚其支持的环境变量
3. Docker-compose模版教程：https://yeasy.gitbook.io/docker_practice/compose/compose_file



## K8S

### 定义：

k8s是一个开源的***容器集群管理系统***，可以实现容器集群的自动化部署、自动扩缩容、维护等功能



1. master集群：api-server、schedular【metadata都存在etcd cluster里】

2. worknode集群：kubernates、pod、containner、kube-proxy（解决动态的路由问题，维护cluster- service ip和动态pod ip的路由表。一个node维护一个虚拟子网，为其中的pod分配虚拟ip）

3. node是物理机；

4. pod是k8s资源调度的最小单位，pod是虚拟机，一个node可包含多个pod；（service ip映射的是pod的ip）

5. containner是容器，一个pod可以包含多个containner（放在一个pod内的containner一般是紧耦合的，一个pod内的containner一定是不一样的。pod是复制的单位，可扩展副本）；

6. https://www.cnblogs.com/kevingrace/p/6655153.html

7. ```
   Why does Kubernetes allow more than one container in a Pod? Containers in a Pod runs on a "logical host": they use the same network namespace (same IP address and port space), IPC namespace and, optionally, they can use shared volumes. Therefore, these containers can efficiently communicate, ensuring data locality. Also, Pods allow managing several tightly coupled application containers as a single unit.
   ```

8. https://linchpiner.github.io/k8s-multi-container-pods.html#:~:text=Why%20does%20Kubernetes%20allow%20more,efficiently%20communicate%2C%20ensuring%20data%20locality
9. https://www.redhat.com/zh/topics/containers/kubernetes-architecture

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20210610160104092.png" alt="image-20210610160104092" style="zoom:50%;" />

<img src="/Users/lugao/Library/Application Support/typora-user-images/image-20210610160123533.png" alt="image-20210610160123533" style="zoom:50%;" />

