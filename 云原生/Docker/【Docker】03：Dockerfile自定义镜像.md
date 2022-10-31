## 1.镜像结构

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包聚合。

我们以MySQL为例，来看看镜像的组成结构：

![img](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/1637479615721-39abc146-9cb1-4291-b2fd-376795c07d16.png)

简单来说，镜像就是在系统函数库、运行环境基础上，添加应用程序文件、配置文件、依赖文件等组合，然后编写好启动脚本打包在一起形成的文件。

也可参考[DockerHub](https://registry.hub.docker.com)中的详细构建步骤：

![Snipaste_2021-12-28_11-08-35](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_11-08-35.png)

![Snipaste_2021-12-28_11-09-39](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_11-09-39.png)

我们要构建镜像，其实就是实现上述打包的过程。

## 2.Dockerfile语法

**Dockerfile**就是一个文本文件，其中包含一个个的**指令(Instruction)**，用指令来说明要执行什么操作来构建镜像。每一个指令都会形成一层Layer。

我们通过Dockerfile告诉Docker，我们的镜像的组成，需要哪些BaseImage、需要拷贝什么文件、需要安装什么依赖、启动脚本是什么，Docker会根据指定的Dockerfile构建镜像。

| 指令       | 说明                                            | 实例                          |
| ---------- | ----------------------------------------------- | ----------------------------- |
| FROM       | 指定基础镜像                                    | FROM ubuntu:18.04             |
| ENV        | 设置环境变量                                    | ENV key value                 |
| COPY       | Copy本地文件到镜像的指定目录                    | COPY ./dome.jar /tmp          |
| RUN        | 执行Linux的Shell指令                            | RUN yum install gcc           |
| EXPOSE     | 指定容器运行时监听的端口，给镜像使用者参考      | EXPOSE 8080                   |
| ENTRYPOINT | 镜像中应用启动的命令，在Docker执行Run命令是调用 | ENTRYPOINT java -jar dome.jar |

更新详细语法说明，请参考官网文档： https://docs.docker.com/engine/reference/builder

## 3.构建Java项目

### 3.1.基于Ubuntu构建Java项目

需求：基于Ubuntu镜像构建一个新镜像，运行一个java项目

-  步骤1：新建一个空文件夹docker-demo
   ![Snipaste_2021-12-28_13-40-41](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_13-40-41.png)
   
-  步骤2：上传web-demo.jar（普通的SpringBoot工程）文件到docker-demo这个目录
   web-dome 项目结构

   ![image-20211228134832130](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228134832130.png)
   
   ![image-20211228134948403](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228134948403.png)
   
-  步骤3：上传JDK安装包和Dockerfile到docker-demo：
   ![image-20211228135918599](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228135918599.png)
   
   Dockerfile的内容如下：  

```dockerfile
# 指定基础镜像
FROM ubuntu:18.04
# 配置环境变量，JDK的安装目录
ENV JAVA_DIR=/usr/local

# 拷贝jdk和web_dome的jar
COPY ./jdk-8u201-linux-x64.tar.gz $JAVA_DIR/
COPY ./web_demo.jar /tmp/app.jar

# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk-8u201-linux-x64.tar.gz \
 && mv ./jdk1.8.0_201 ./java8

# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin

# 暴露端口
EXPOSE 8090

# 入口，java项目的启动命令
ENTRYPOINT java -jar /tmp/app.jar
```

-  步骤4：进入文件夹运行命令：  

```sh
docker build -t javaweb:1.0 .
```

![Snipaste_2021-12-28_14-04-01](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_14-04-01.png)

构建成功，执行命令`docker images`查看镜像：

![Snipaste_2021-12-28_14-05-10](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_14-05-10.png)

Docker 运行镜像：

```sh
docker run -d --name javaweb -p 8090:8090 javaweb:1.0
```

![Snipaste_2021-12-28_14-07-20](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/Snipaste_2021-12-28_14-07-20.png)

最后访问 http://IP:8090/hello，其中的ip改成你的虚拟机IP:

![image-20211228140756744](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228140756744.png)

### 3.2.基于已存在的镜像构建

例如，构建上面的项目的镜像，可以在已经准备了JDK的基础镜像基础上构建。

1. 创建新目录，复制web_dome.jar到新目录并创建新的Dockerfile，写入以下内容：

```dockerfile
FROM java:8-alpine
COPY ./web_dome.jar /tmp/app.jar
EXPOSE 8090
ENTRYPOINT java -jar /tmp/app.jar
```

​	![image-20211228141926792](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228141926792.png)

2. Docker build构建镜像：

    ```sh
    docker build -t javaweb:2.0 .
    ```

    ![image-20211228142018819](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228142018819.png)

3. Docker 运行镜像：

    ```sh
    docker run -d --name javaweb2.0 -p 8091:8090 javaweb:2.0
    ```

    ![image-20211228142051144](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228142051144.png)

4. 访问接口：http://IP:8091/hello
    ![image-20211228141711859](./assets/%E3%80%90Docker%E3%80%9103%EF%BC%9ADockerfile%E8%87%AA%E5%AE%9A%E4%B9%89%E9%95%9C%E5%83%8F/image-20211228141711859.png)

## 4. 小结

1.  Dockerfile的本质是一个文件，通过指令描述镜像的构建过程 
2.  Dockerfile的第一行必须是FROM，从一个基础镜像来构建 

1.  基础镜像可以是基本操作系统，如Ubuntu。也可以是制作好的镜像，例如：java:8-alpine 