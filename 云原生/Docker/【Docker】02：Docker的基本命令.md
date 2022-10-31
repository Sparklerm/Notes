## 1.镜像操作

### 1.1.镜像名称

首先来看下镜像的名称组成：

- 镜名称一般分两部分组成：[repository]:[tag]。
- 在没有指定tag时，默认是latest，代表最新版本的镜像

如图：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479515389-88bd3b3e-8ac8-4879-9b85-a8b988390ecc.png)

这里的mysql就是repository，5.7就是tag，合一起就是镜像名称，代表5.7版本的MySQL镜像。

### 1.2.镜像命令

常见的镜像操作命令如图：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479520361-79005c1d-febf-4b23-800b-e3f788b2f747.png)



### 1.3. 拉取、查看镜像

1）首先去镜像仓库搜索nginx镜像，比如[DockerHub](https://hub.docker.com/):

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1639984743076-4819e7c4-09a0-4dfa-8aad-63137f4c57be.png)



2）根据查看到的镜像名称，拉取自己需要的镜像，通过命令：docker pull nginx

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1639987659591-bd721a2c-dd89-4fd9-9255-a89e4477c997.png)

3）通过命令：docker images 查看拉取到的镜像

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1639987696071-35e72a90-578c-4b08-868f-1b70c2dd3c81.png)

### 1.4 保存、导入镜像

1）利用docker xx --help命令查看docker save和docker load的语法

例如，查看save命令用法，可以输入命令：

```sh
docker save --help
```

结果：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479540942-d137017a-7b68-4fcd-9661-5c5f2a8fb2d0.png)

命令格式：

```shell
docker save -o [保存的目标文件名称] [镜像名称]
```

2）使用docker save导出镜像到磁盘

运行命令：

```sh
docker save -o nginx.tar nginx:latest
```

结果如图：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1639988023330-89eb6243-5023-4c43-afec-6eeb2d53176e.png)

3）使用docker load加载镜像

先删除本地的nginx镜像：

```sh
docker rmi nginx:latest
```

然后运行命令，加载本地文件：

```sh
docker load -i nginx.tar
```

结果：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479551696-6d80ddf2-2266-4c2f-a8c1-72e9ae0033af.png)

## 2.容器操作

### 2.1.容器相关命令

容器操作的命令如图：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479558066-e1d52fa6-0703-4c06-a2fd-74226efdbe85.png)



容器保护三个状态：

- 运行：进程正常运行
- 暂停：进程暂停，CPU不再运行，并不释放内存

- 停止：进程终止，回收进程占用的内存、CPU等资源

其中：

-  docker run：创建并运行一个容器，处于运行状态 
-  docker pause：让一个运行的容器暂停 

-  docker unpause：让一个容器从暂停状态恢复运行 
-  docker stop：停止一个运行的容器 

-  docker start：让一个停止的容器再次运行 
-  docker rm：删除一个容器 

### 2.2 创建并运行一个容器

创建并运行nginx容器的命令：

```sh
docker run --name containerName -p 80:80 -d nginx
```

命令解读：

- docker run ：创建并运行一个容器
- --name : 给容器起一个名字，比如叫做mn

- -p ：将宿主机端口与容器端口映射，冒号左侧是宿主机端口，右侧是容器端口
- -d：后台运行容器

- nginx：镜像名称，例如nginx

这里的`-p`参数，是将容器端口映射到宿主机端口。

默认情况下，容器是隔离环境，我们直接访问宿主机的80端口，肯定访问不到容器中的nginx。

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1640007685589-9342660f-710e-4251-a6bb-74e5e54742a2.png)

查看容器状态：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1640008111072-6488ec68-f84a-4010-8568-b81d36004efa.png)

现在，将容器的80与宿主机的80关联起来，当我们访问宿主机的80端口时，就会被映射到容器的80，这样就能访问到nginx了：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1640007394823-6bd70ee9-29aa-4d9f-8666-c02cf9bcdc96.png)

访问Nginx：http://ip:80

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1640008545981-0782b407-13ce-4216-8251-ba6c87096e27.png)

### 2.3.案例-进入容器，修改文件

进入Nginx容器，修改Nginx的Index文件内容，查看效果，添加“Hello Docker！”

进入容器要用到docker exec命令。

**步骤**：

1）进入容器。进入我们刚刚创建的nginx容器的命令为：

```sh
docker exec -it mn bash
```

命令解读：

-  docker exec ：进入容器内部，执行一个命令 
-  -it : 给当前进入的容器创建一个标准输入、输出终端，允许我们与容器交互 

-  mn ：要进入的容器的名称 
-  bash：进入容器后执行的命令，bash是一个linux终端交互命令 

2）进入nginx的HTML所在目录 /usr/share/nginx/html

容器内部会模拟一个独立的Linux文件系统，看起来如同一个linux服务器一样：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479568871-3273d0d5-0acc-47ea-a6d0-9f2631a8114f.png)

nginx的环境、配置、运行文件全部都在这个文件系统中，包括我们要修改的html文件。

查看DockerHub网站中的nginx页面，可以知道nginx的html目录位置在`/usr/share/nginx/html`

我们执行命令，进入该目录：

```sh
cd /usr/share/nginx/html
```

查看目录下文件：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479573703-a7907378-26a8-4f69-8784-48833f1199c3.png)

3）修改index.html的内容

容器内没有vi命令，无法直接修改，我们用下面的命令来修改：

```sh
sed -i -e 's#Welcome to nginx#Hello Docker!#g' -e 's#<head>#<head><meta charset="utf-8">#g' index.html
```

在浏览器访问自己的虚拟机地址，例如我的是：http://192.168.150.101，即可看到结果：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1640009089073-4b807c9f-b20f-4e19-8917-2218b5540ac5.png)

### 2.4.小结

1. docker run命令的常见参数有哪些？

- - --name：指定容器名称
  - -p：指定端口映射

- - -d：让容器后台运行

1. 查看容器日志的命令：

- - docker logs
  - 添加 -f 参数可以持续查看日志

1. 查看容器状态：

- - docker ps
  - docker ps -a 查看所有容器，包括已经停止的

## 3.数据卷（容器数据管理）

在之前的nginx案例中，修改nginx的html页面时，需要进入nginx内部。并且因为没有编辑器，修改文件也很麻烦。

这就是因为容器与数据（容器内文件）耦合带来的后果。

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479584585-8a6c34bd-b0a4-466f-9993-02dc686e88f6.png)

要解决这个问题，必须将数据与容器解耦，这就要用到数据卷了。

### 3.1 什么是数据卷

**数据卷（volume）**是一个虚拟目录，指向宿主机文件系统中的某个目录。

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479589127-74f41574-4066-4bbe-85b2-86eca160c170.png)

一旦完成数据卷挂载，对容器的一切操作都会作用在数据卷对应的宿主机目录了。

这样，我们操作宿主机的/var/lib/docker/volumes/html目录，就等于操作容器内的/usr/share/nginx/html目录了

### 3.2.数据集操作命令

数据卷操作的基本语法如下：

```sh
docker volume [COMMAND]
```

docker volume命令是数据卷操作，根据命令后跟随的command来确定下一步的操作：

- create 创建一个volume
- inspect 显示一个或多个volume的信息

- ls 列出所有的volume
- prune 删除未使用的volume

- rm 删除一个或多个指定的volume

### 3.3.创建和查看数据卷

**需求**：创建一个数据卷，并查看数据卷在宿主机的目录位置

① 创建数据卷

```sh
docker volume create html
```

② 查看所有数据

```sh
docker volume ls
```

结果：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479594766-cfa3bffd-d631-4b25-b551-cefe757245bf.png)

③ 查看数据卷详细信息卷

```sh
docker volume inspect html
```

结果：

![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479598749-9b2dc540-e20d-41e2-b0a8-fa989d446b30.png)

可以看到，我们创建的html这个数据卷关联的宿主机目录为`/var/lib/docker/volumes/html/_data`目录。



**小结**：

数据卷的作用：

- 将容器与数据分离，解耦合，方便操作容器内数据，保证数据安全

数据卷操作：

- docker volume create：创建数据卷
- docker volume ls：查看所有数据卷

- docker volume inspect：查看数据卷详细信息，包括关联的宿主机目录位置
- docker volume rm：删除指定数据卷

- docker volume prune：删除所有未使用的数据卷

### 3.4.挂载数据卷

我们在创建容器时，可以通过 -v 参数来挂载一个数据卷到某个容器内目录，命令格式如下：

```sh
docker run \
  --name mn \
  -v html:/root/html \
  -p 8080:80
  nginx \
```

这里的-v就是挂载数据卷的命令：

- `-v html:/root/htm` ：把html数据卷挂载到容器内的/root/html这个目录中

### 3.5.案例-给nginx挂载数据卷

**需求**：创建一个nginx容器，修改容器内的html目录内的index.html内容

**分析**：上个案例中，我们进入nginx容器内部，已经知道nginx的html目录所在位置/usr/share/nginx/html ，我们需要把这个目录挂载到html这个数据卷上，方便操作其中的内容。

**提示**：运行容器时使用 -v 参数挂载数据卷

步骤：

① 创建容器并挂载数据卷到容器内的HTML目录

```sh
docker run --name mn -v html:/usr/share/nginx/html -p 80:80 -d nginx
```

② 进入html数据卷所在位置，并修改HTML内容

```sh
# 查看html数据卷的位置
docker volume inspect html
# 进入该目录
cd /var/lib/docker/volumes/html/_data
# 修改文件
vi index.html
```

### 3.6.案例-给MySQL挂载本地目录

容器不仅仅可以挂载数据卷，也可以直接挂载到宿主机目录上。关联关系如下：

- 带数据卷模式：宿主机目录 --> 数据卷 ---> 容器内目录
- 直接挂载模式：宿主机目录 ---> 容器内目录

如图：![img](./assets/%E3%80%90Docker%E3%80%9102%EF%BC%9ADocker%E5%9F%BA%E6%9C%AC%E5%91%BD%E4%BB%A4/1637479606630-28d54b26-1fcf-47b2-b539-bd61fb7f0798.png)

**语法**：

目录挂载与数据卷挂载的语法是类似的：

- -v [宿主机目录]:[容器内目录]
- -v [宿主机文件]:[容器内文件]



**需求**：创建并运行一个MySQL容器，将宿主机目录直接挂载到容器

实现思路如下：

1）在将课前资料中的mysql.tar文件上传到虚拟机，通过load命令加载为镜像

2）创建目录/tmp/mysql/data

3）创建目录/tmp/mysql/conf，将课前资料提供的hmy.cnf文件上传到/tmp/mysql/conf

4）去DockerHub查阅资料，创建并运行MySQL容器，要求：

① 挂载/tmp/mysql/data到mysql容器内数据存储目录

② 挂载/tmp/mysql/conf/hmy.cnf到mysql容器的配置文件

③ 设置MySQL密码

### 3.7.小结

docker run的命令中通过 -v 参数挂载文件或目录到容器中：

- -v volume名称:容器内目录
- -v 宿主机文件:容器内文件

- -v 宿主机目录:容器内目录

数据卷挂载与目录直接挂载的

- 数据卷挂载耦合度低，由docker来管理目录，但是目录较深，不好找
- 目录挂载耦合度高，需要我们自己管理目录，不过目录容易寻找查看