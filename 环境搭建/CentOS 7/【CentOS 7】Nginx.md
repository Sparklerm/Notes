1. 安装前置依赖

   ```shell
   yum install gcc-c++ pcre-devel openssl openssl-devel make -y
   ```

2. 创建文件夹

   ```
   cd /usr/local
   mkdir nginx
   ```

3. 解压安装文件

   ```shell
   cd nginx
   tar -zxvf /xxx/nginx-x.x.x.tar.gz -C ./
   ```

4. 编译安装

   ```shell
   cd nginx-x.x.x
   ./configure
   make && make install
   ```

5. 查看安装后的文件

   ```shell
   cd /usr/local/nginx
   # 可执行文件位于 /usr/local/nginx/sbin/nginx
   ```

6. 删除安装文件

   ```shell
   cd /usr/local/nginx
   rm -rf nginx-x.x.x
   ```

7. 启动Nginx

   ```shell
   /usr/local/nginx/sbin/nginx
   # 停止Nginx
   /usr/local/nginx/sbin/nginx -s stop
   # 重载Nginx
   /usr/local/nginx/sbin/nginx -s reload
   ```

8. 配置文件位于

   ```
   /usr/local/nginx/conf/nginx.conf
   ```

9. 配置Nginx开机自启

   ```shell
   # 创建nginx service
   cd /usr/lib/systemd/system
   touch nginx.service
   # 编辑文件 写入以下内容，路径根据自己的安装路径修改
   vi nginx.service
   ```

   ```
   [Unit]
   Description=nginx
   After=network.target
   
   [Service]
   Type=forking
   ExecStart=/usr/local/tengine/sbin/nginx
   ExecReload=/usr/local/tengine/sbin/nginx -s reload
   ExecStop=/usr/local/tengine/sbin/nginx -s quit
   PrivateTmp=true
   
   [Install]
   WantedBy=multi-user.target
   ```

   ```shell
   # 重载服务
   systemctl daemon-reload
   ```

   ```shell
   # 开机自启
   systemctl enable nginx.service
   # 关闭开机自启动
   systemctl disable nginx.service
   ```

   ```shell
   # 服务命令
   systemctl start nginx.service   #开启
   systemctl stop nginx.service    #关闭
   systemctl reload nginx.service  #重新加载配置
   systemctl status nginx.service  #查看状态
   ```

