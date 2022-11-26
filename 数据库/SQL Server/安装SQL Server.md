# 安装SQL Server

## 1. 安装要求

### 1. 环境检查

1. 是否已安装SQL Server。
   1. 打开Windows 菜单，查看是否存在已安装的SQL Server实例。
      <img src="assets/安装SQL Server/13.png" alt="13" style="zoom: 50%;" />
   2. 打开SQL Server安装工具，查看本地实例。
      <img src="assets/安装SQL Server/111.png" alt="111" style="zoom: 50%;" />
      <img src="assets/安装SQL Server/1112.png" alt="1112" style="zoom:50%;" />
2. SQL Server可以安装多实例，确定启用的实例。
3. 准备安装包。

### 2. 安装目标

1. 数据库服务正常运行。
2. 可远程访问数据库。
3. 开启账号远程登录。

## 2. 安装

### 1. 解压安装包，获得安装文件。
<img src="assets/安装SQL Server/image-20221120161620958.png" alt="image-20221120161620958" style="zoom:50%;" />

### 2. 启动安装程序“SETUP”，选择“全新SQL Server独立安装”
<img src="assets/安装SQL Server/1.png" alt="1" style="zoom:50%;" />

### 3. 具体安装步骤

#### 1. 选择安装版本，或通过密钥自动匹配版本。
<img src="assets/安装SQL Server/2.png" alt="2" style="zoom:50%;" />

#### 2. 同意安装条约。

<img src="assets/安装SQL Server/3.png" alt="3" style="zoom:50%;" />

#### 3. 自动更新，按需选择。
<img src="assets/安装SQL Server/4.png" alt="4" style="zoom:50%;" />

#### 4. 安装环境检测。
<img src="assets/安装SQL Server/5.png" alt="5" style="zoom:50%;" />

#### 5. 安装功能选择和数据库安装目录选择。
<img src="assets/安装SQL Server/6.png" alt="6" style="zoom:50%;" />

#### 6. SQL Server实例基础信息配置。
<img src="assets/安装SQL Server/7.png" alt="7" style="zoom:50%;" />

#### 7. SQL Server Service账户和排序规则配置。
<img src="assets/安装SQL Server/8.png" alt="8" style="zoom:50%;" />
<img src="assets/安装SQL Server/9.png" alt="9" style="zoom:50%;" />

#### 8. SQL Server 账户密码设置。
<img src="assets/安装SQL Server/10-1.png" alt="10-1" style="zoom:50%;" />

#### 9. 数据存放目录配置。
<img src="assets/安装SQL Server/10-2.png" alt="10-2" style="zoom:50%;" />

#### 10. 数据库硬件资源配置。

1. CPU资源
   <img src="assets/安装SQL Server/10-3.png" alt="10-3" style="zoom:50%;" />
2. 内存资源。
   <img src="assets/安装SQL Server/10-4.png" alt="10-4" style="zoom:50%;" />

#### 11. 确认配置项，开始安装。
<img src="assets/安装SQL Server/11.png" alt="11" style="zoom:50%;" />

#### 12. 安装完成结果。
<img src="assets/安装SQL Server/12.png" alt="12" style="zoom:50%;" />
<img src="assets/安装SQL Server/13-166893573788021.png" alt="13" style="zoom:50%;" />

#### 13. 配置SQL Server远程访问。

1. 打开SQL Server配置管理器
   <img src="assets/安装SQL Server/14.png" alt="14" style="zoom:50%;" />

2. 选择网络配置。

   1. 启用TCP协议（远程访问协议）。

      <img src="assets/安装SQL Server/15.png" alt="15" style="zoom:50%;" />
      <img src="assets/安装SQL Server/15-1.png" alt="15-1" style="zoom:50%;" />

   2. 配置访问信息。
      <img src="assets/安装SQL Server/15-3.png" alt="15-3" style="zoom:50%;" />

   3. 保存并重启SQL Server。
      <img src="assets/安装SQL Server/15-2.png" alt="15-2" style="zoom:50%;" />
      <img src="assets/安装SQL Server/15-4.png" alt="15-4" style="zoom:50%;" />
      <img src="assets/安装SQL Server/16.png" alt="16" style="zoom:50%;" />

#### 14. 配置防火墙，开放SQL Server访问端口。

1. 尝试连接SQL Server。
   <img src="assets/安装SQL Server/17-1.png" alt="17-1" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-2.png" alt="17-2" style="zoom:50%;" />
2. 打开防火墙配置。
   <img src="assets/安装SQL Server/17-3.png" alt="17-3" style="zoom:50%;" />
3. 配置防火墙规则
   <img src="assets/安装SQL Server/17-4.png" alt="17-4" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-5.png" alt="17-5" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-6.png" alt="17-6" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-7.png" alt="17-7" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-8.png" alt="17-8" style="zoom:50%;" />
   <img src="assets/安装SQL Server/17-9.png" alt="17-9" style="zoom:50%;" />
4. 再次连接。
   ![17-1](C:/Users/Sparkler/Desktop/aa/17-1.png)
   ![17-10](C:/Users/Sparkler/Desktop/aa/17-10.png)



