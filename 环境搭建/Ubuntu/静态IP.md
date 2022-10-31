使用netplan设置静态IP（推荐）

```shell
# ubuntu20.04推荐使用netplan命令设置静态IP地址。
# netplan使用yaml格式的配置文件来配置网络地址。
vim /etc/netplan/01-network-manager-all.yaml
```

在01-network-manager-all.yaml文件中renderer那一行后面添加以下内容。

```
ethernets:
    ens33:
      addresses: [192.168.2.116/24]
      gateway4: 192.168.2.1
      nameservers:
        addresses: [192.168.2.1,8.8.8.8]
```

完成后的文件是下面这个样子。

```
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      addresses: [192.168.2.116/24]
      gateway4: 192.168.2.1
      nameservers:
        addresses: [192.168.2.1,8.8.8.8]
```

📓 注意yaml文件是通过缩进来控制上下层级关系的，你的缩进可以是一个空格也可以是N个空格，但要在整个配置文件中要保持一致，并要注意在冒号的后面加一个空格。
让配置文件生效。

```
netplan apply
```

