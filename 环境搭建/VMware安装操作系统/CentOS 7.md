

# 准备

>   安装VMware：[网盘分享](https://www.aliyundrive.com/s/ffN4inSTDda)
>
>   下载CentOS 7 镜像：[Aliyun镜像站下载](https://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso)

# VMware创建CentOS 7虚拟机

## 镜像选择

![Snipaste_2022-01-01_18-59-32](./assets/CentOS%207/Snipaste_2022-01-01_18-59-32.png)

![Snipaste_2022-01-01_18-59-59](./assets/CentOS%207/Snipaste_2022-01-01_18-59-59.png)

![Snipaste_2022-01-01_19-00-24](./assets/CentOS%207/Snipaste_2022-01-01_19-00-24.png)

![Snipaste_2022-01-01_19-01-47](./assets/CentOS%207/Snipaste_2022-01-01_19-01-47.png)

![Snipaste_2022-01-01_19-02-42](./assets/CentOS%207/Snipaste_2022-01-01_19-02-42.png)

## 虚拟机配置

>   CPU与内存可使用默认配置，也可按照宿主机性能进行配置，推荐CPU:RAM=1:2
>
>   CPU 和 内存的配置并不意味着分配后宿主机就会直接扣减这部分的性能，这些配置只是限制虚拟机可使用资源的最大值。

![Snipaste_2022-01-01_19-04-10](./assets/CentOS%207/Snipaste_2022-01-01_19-04-10.png)

![Snipaste_2022-01-01_19-05-06](./assets/CentOS%207/Snipaste_2022-01-01_19-05-06.png)

## 虚拟机网络

>   **`桥接网络`**：可直接访问外网，也可被外网访问。
>
>   **`NAT网络`**：共享宿主机的网络，可访问外网，但是只能被同NAT网络域的虚拟机及宿主机访问。
>
>   没有特殊的要求可直接选择NAT网络。

![Snipaste_2022-01-01_19-08-42](./assets/CentOS%207/Snipaste_2022-01-01_19-08-42.png)

## 磁盘配置

>   磁盘容量按需选择就好，其他选项可默认。
>
>   磁盘容量设置为虚拟机允许使用的最大磁盘容量，随着虚拟机文件增加而占用增大。

![Snipaste_2022-01-01_19-09-09](./assets/CentOS%207/Snipaste_2022-01-01_19-09-09.png)

![Snipaste_2022-01-01_19-09-21](./assets/CentOS%207/Snipaste_2022-01-01_19-09-21.png)

![Snipaste_2022-01-01_19-09-31](./assets/CentOS%207/Snipaste_2022-01-01_19-09-31.png)

![Snipaste_2022-01-01_19-09-48](./assets/CentOS%207/Snipaste_2022-01-01_19-09-48.png)

![Snipaste_2022-01-01_19-10-11](./assets/CentOS%207/Snipaste_2022-01-01_19-10-11.png)

## 虚拟机虚拟硬件配置

>    移除一些不必要的虚拟硬件可一定程度上减小虚拟机对宿主机资源的占用。

![Snipaste_2022-01-01_19-10-24](./assets/CentOS%207/Snipaste_2022-01-01_19-10-24.png)

![Snipaste_2022-01-01_19-12-06](./assets/CentOS%207/Snipaste_2022-01-01_19-12-06.png)

![Snipaste_2022-01-01_19-13-05](./assets/CentOS%207/Snipaste_2022-01-01_19-13-05.png)

![Snipaste_2022-01-01_19-13-27](./assets/CentOS%207/Snipaste_2022-01-01_19-13-27-16410371617301.png)

# 安装CentOS 7

## 选择Install CentOS 7 进行安装

![Snipaste_2022-01-01_19-14-20](./assets/CentOS%207/Snipaste_2022-01-01_19-14-20.png)

## 选择系统语言

![image-20220101195418698](./assets/CentOS%207/image-20220101195418698.png)

## 安装选项

![image-20220101195400404](./assets/CentOS%207/image-20220101195400404.png)

![Snipaste_2022-01-01_19-18-19](./assets/CentOS%207/Snipaste_2022-01-01_19-18-19.png)

## 磁盘设置

![Snipaste_2022-01-01_19-18-43](./assets/CentOS%207/Snipaste_2022-01-01_19-18-43.png)

## 网络设置

>   1.   直接开启网络开关；
>   2.   开启网络开关后进行高级配置（如静态IP）；

![Snipaste_2022-01-01_19-20-00](./assets/CentOS%207/Snipaste_2022-01-01_19-20-00.png)

![Snipaste_2022-01-01_19-21-14](./assets/CentOS%207/Snipaste_2022-01-01_19-21-14.png)

![Snipaste_2022-01-01_19-24-36](./assets/CentOS%207/Snipaste_2022-01-01_19-24-36.png)



![Snipaste_2022-01-01_19-24-56](./assets/CentOS%207/Snipaste_2022-01-01_19-24-56.png)

## 开始安装

![Snipaste_2022-01-01_19-25-09](./assets/CentOS%207/Snipaste_2022-01-01_19-25-09.png)

![Snipaste_2022-01-01_19-25-23](./assets/CentOS%207/Snipaste_2022-01-01_19-25-23.png)

![Snipaste_2022-01-01_19-26-05](./assets/CentOS%207/Snipaste_2022-01-01_19-26-05.png)

![Snipaste_2022-01-01_19-30-21](./assets/CentOS%207/Snipaste_2022-01-01_19-30-21.png)

## 安装完成登录系统

![Snipaste_2022-01-01_19-32-11](./assets/CentOS%207/Snipaste_2022-01-01_19-32-11.png)



## 使用工具连接（xshell）

**文件 —— 新建**

### 配置连接参数

![Snipaste_2022-01-01_19-33-03](./assets/CentOS%207/Snipaste_2022-01-01_19-33-03.png)



![Snipaste_2022-01-01_19-33-16](./assets/CentOS%207/Snipaste_2022-01-01_19-33-16.png)

### 密钥可直接保存方便下次连接虚拟机

![Snipaste_2022-01-01_19-33-34](./assets/CentOS%207/Snipaste_2022-01-01_19-33-34.png)

### 连接成功

![Snipaste_2022-01-01_19-33-53](./assets/CentOS%207/Snipaste_2022-01-01_19-33-53.png)
