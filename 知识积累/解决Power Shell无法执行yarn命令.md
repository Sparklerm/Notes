1. 以管理员的身份运行 powershell, 步骤是 左下角开始--》输入 powershell --》以管理员身份运行
2. 输入 set-ExecutionPolicy RemoteSigned 命令， 再输入 Y 或者 A 选 是 或 全部
3. 输入 get-ExecutionPolicy 查看修改结果 RemoteSigned 表示成功
4. 输入 yarn --version 检查是否成功。