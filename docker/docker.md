## Docker

### 简介

docker是一个开源的应用容器引擎

docker可以让开发这打包他们的应用以及依赖包到一个轻量级、可移植的容器中。然后发布到任何流行的linux机器上，也可以实现虚拟化

容器是完全使用沙箱机制，相互之间不会有任何接口，更重要的是容器性能开销降低。

docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像。运行中的这个镜像称为容器，容器启动是非常快速的。类似windows里面的ghost操作系统，安装好后什么都有了	 

 ### 核心概念

* docker主机（host）：安装了docker程序的机器（docker直接安装在操作系统之上）
* docker客户端（client）：连接docker主机进行操作
* docker仓库（registry）：软件打包好的镜像，放在docker仓库中
* docker容器（container）：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

### 使用步骤

1. 安装docker
2. 去docker仓库找到这个软件对应的镜像
3. 使用docker运行这个镜像，这个镜像就会生成一个docker容器
4. 对容器的启动停止就是对软件的启动停止

### 在linux虚拟机上安装docker

```
1. 检查内核版本，必须是3.10及以上
uname -r
2. 安装docker
yum install docker
3. 启动docker
systemctl start docker
docker -v
4. 开启启动docker
systemctl enbale docker
5.停止docker
systemctl stop docker
```

### 容器操作

1. 搜索镜像

    ```bash
    docker search mysql
    ```

2.  拉取镜像

    ````bash
    docker pull mysql
    ````

3. 根据镜像启动容器

    ```bash
    docker run --name mymysql -d mysql:latest
    ```

4. 查看运行中的容器

    ```bash
    docker ps
    ```

5. 停止运行中的容器

    ```bash
    docker stop 容器的id
    ```

6. 查看所有的容器

    ```bash
    docker ps -a
    ```

7. 启动容器

    ```bash
    docker start 容器id
    ```

8. 删除容器

    ```bash
    docker rm 容器id
    ```

9.  启动一个做了端口映射的tomcat

    ```bash
    docker run -d -p 8888:8080 tomcat
    -d：后台运行
    -p：将主机架的端口映射到容器的一个端口，主机端口：容器内部的端口
    ```

10. 查看容器的日志

    ```bash
    docker logs 容器名或者id 
    ```

    