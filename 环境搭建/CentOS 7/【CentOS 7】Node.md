我这⾥下载的是 node-v12.16.3-linux-x64.tar.xz 安装包，并将其直接放在了 root ⽬录下 


创建⽬录并解压 
 1、在 /usr/local/ 下创建 node ⽂件夹并进⼊ 


```plain
cd /usr/local/
mkdir node
cd node
```

2、将 Node 的安装包解压到 /usr/local/node 中即可 


```plain
[root@localhost node]# tar -xJvf /root/node-v12.16.3-linux-x64.tar.xz -C ./
```

解压完之后， /usr/local/node ⽬录中会出现⼀个 node-v12.16.3-linux-x64 的⽬录 


配置NODE系统环境变量 
 编辑 ~/.bash_profile ⽂件，在⽂件末尾追加如下信息: 

```plain
# Nodejs
export PATH=/usr/local/node/node-v12.16.3-linux-x64/bin:$PATH
```

![img](./assets/%E3%80%90CentOS%207%E3%80%91Node/1639735298612-fe1e0671-3562-47cc-9390-ccad23918083.png)

刷新环境变量，使之⽣效即可： 


```plain
source ~/.bash_profile
```

检查安装结果 


```plain
node -v
npm version
npx -v
```

均有版本信息输出即可： 
 ![img](./assets/%E3%80%90CentOS%207%E3%80%91Node/1639735342378-1114fd88-9f83-4c55-b17f-0016685ffc7e.png)