CentOS 7.4 默认⾃带了⼀个 Python2.7 环境： 
![img](./assets/%E3%80%90CentOS%207%E3%80%91Python/1639735376785-cf46fa13-9cde-425b-942b-761bc40d043c.png)

然⽽现在主流都是 Python3 ，所以接下来再装⼀个 Python3 ，打造⼀个共存的环境。 


准备PYTHON3安装包并解压
我这⾥下载的是 Python-3.8.3.tgz 安装包，并将其直接放在了 /root ⽬录下
执⾏如下命令解压之： `tar zxvf Python-3.8.3.tgz `
 则可以在当前⽬录得到⽂件夹： Python-3.8.3 

安装相关预备环境 
 直接执⾏如下命令即可： 

```sh
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlitedevel readline-devel tk-devel gcc make
```

编译并安装 
 这⾥指定了安装⽬录为 /usr/local/python3 ，有需要可以⾃定义 

```sh
cd Python-3.8.3/
./configure prefix=/usr/local/python3
make && make install
```

等安装过程完毕， /usr/local/python3 ⽬录就会⽣成了 


添加软链接
我们还需要将刚刚安装⽣成的⽬录 /usr/local/python3 ⾥的 python3 可执⾏⽂件做⼀份软链接，链接到 /usr/bin 下，⽅便后续使⽤python3 


```sh
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

验证安装
命令⾏输⼊ `python3 `，即可查看 Python3 版本的安装结果 
![img](./assets/%E3%80%90CentOS%207%E3%80%91Python/1639735639364-17b6881f-e3cf-48b9-bac7-76926e0e9319.png)

⽽输⼊ `python `，依然还是 python2.7.5 环境 
![img](./assets/%E3%80%90CentOS%207%E3%80%91Python/1639735650614-4644cf05-fb08-489e-8e6b-c35c01d34e0a.png)