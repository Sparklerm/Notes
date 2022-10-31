# Windwos Server 试用版转正式版（更新时间2022-10-26）

## Windows Server 2016

1. 以管理员身份运行PowerShell

2. 输入`DISM /online /Get-CurrentEdition`查看Edition ID。

   > 什么是如果是“Edition ID” ？
   >
   > Evaluation 的话，例如 Standard 标准版评估版本，一般就是*ServerStandardEval 把后面 Eval 那四字母去了就是你的 Edition ID。*

3. 根据系统版本选择转换指令

   > 标准版：ServerStandard
   >
   > DISM /online /Set-Edition:ServerStandard /ProductKey:WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY /AcceptEula
   >
   > 数据中心版：ServerDatacenter
   >
   > DISM /online /Set-Edition:ServerDatacenter /ProductKey:CB7KF-BWN84-R7R2Y-793K2-8XDDG /AcceptEula



## Windows Server 2019

1. 以管理员身份运行PowerShell

2. 输入`DISM /online /Get-CurrentEdition`查看Edition ID。

   > 什么是如果是“Edition ID” ？
   >
   > Evaluation 的话，例如 Standard 标准版评估版本，一般就是*ServerStandardEval 把后面 Eval 那四字母去了就是你的 Edition ID。*

3. 根据系统版本选择转换指令

   > 标准版：ServerStandard
   >
   > dism /online /set-edition:ServerStandard /productkey:N69G4-B89J2-4G8F4-WWYCC-J464C /accepteula
   >
   > 数据中心版：ServerDatacenter
   >
   > dism /online /set-edition:ServerDatacenter /productkey:WMDGN-G9PQG-XVVXX-R3X43-63DFG /accepteula



## Windows Server 2022

> 数据中心版密钥：WX4NM-KYWYW-QJJR4-XV3QB-6VM33



## 番外：

> 此方法也适用于 Standard 转 DataCenter （2016）
>
> 比如将标准版转换为 Datacenter 版本，执行如下命令：
>
> DISM /online /Set-Edition:ServerDatacenter /ProductKey:XXXXX-XXXXX-XXXXX-XXXXX-XXXXX /AcceptEula

## 所用到的序列号

Windows Server 2016 数据中心：
CB7KF-BWN84-R7R2Y-793K2-8XDDG

Windows Server 2016 标准版：
WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY