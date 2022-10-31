# 配置文件

找到CentOS7下对应配置文件，目录为：/etc/systemd/logind.conf，使用vim[命令](https://www.linuxcool.com/)打开

```bash
vim /etc/systemd/logind.conf
```

# 修改配置

配置文件中找到我们要修改的配置项：

>   HandlePowerKey 按下电源键后的行为，默认power off
>   HandleSleepKey 按下挂起键后的行为，默认suspend
>   HandleHibernateKey 按下休眠键后的行为，默认hibernate
>   HandleLidSwitch 合上笔记本盖后的行为，默认suspend

我们把HandleLidSwitch后面的suspend修改为lock，即：

```
HandleLidSwitch=lock
```

注意，如果配置项前面有#号，要删掉，#是注释的意思

其中，后面的配置项的可选范围为：

>   ignore 忽略，跳过
>   power off 关机
>   eboot 重启
>   halt 挂起

suspend [shell](https://www.linuxcool.com/)内建指令，可暂停目前正在执行的shell。若要恢复，则必须使用SIGCONT信息。所有的进程都会暂停，但不是消失（halt是进程关闭）
 hibernate 让笔记本进入休眠状态
 hybrid-sleep  混合睡眠，主要是为台式机设计的，是睡眠和休眠的结合体，当你选择Hybird时，系统会像休眠一样把内存里的数据从头到尾复制到硬盘里  ，然后进入睡眠状态，即内存和CPU还是活动的，其他设置不活动，这样你想用电脑时就可以快速恢复到之前的状态了，笔记本一般不用这个功能。
 lock 仅锁屏，计算机继续工作。

# 应用生效

必须要使用如下[命令](https://www.linuxcool.com/)才能使上面的配置生效

```
systemctl restart systemd-logind
```

