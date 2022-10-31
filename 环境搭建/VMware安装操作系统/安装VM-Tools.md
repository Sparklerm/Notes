```bash
# 卸载自带的open-vm-tools
yum remove -y open-vm-tools
# 安装依赖包
yum update -y
yum install kernel-devel-$(uname -r) -y
yum -y install perl gcc gcc-c++ make cmake kernel kernel-headers kernel-devel net-tools
# 解压安装包
tar -zxvf vm-tools.tar.gz
# 安装
cd vm-tools
./vmware-install.pl
```

