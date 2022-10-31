# CnetOS 7 安装 GitLab

#### 1. 安装和配置必须的依赖项

在 CentOS 7上，下面的命令也会在系统防火墙中打开 HTTP、HTTPS 和 SSH 访问。这是一个可选步骤，如果您打算仅从本地网络访问极狐GitLab，则可以跳过它。

```shell
sudo yum install -y curl policycoreutils-python openssh-server perl
sudo systemctl enable sshd
sudo systemctl start sshd

sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
```

（可选）下一步，安装 Postfix 以发送电子邮件通知。如果您想使用其他解决方案发送电子邮件，请跳过此步骤并在安装极狐GitLab 后[配置外部 SMTP 服务器](https://docs.gitlab.cn/omnibus/settings/smtp.html)。

```shell
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

在安装 Postfix 的过程中可能会出现一个配置界面，在该界面中选择“Internet Site”并按下回车。把“mail name”设置为您服务器的外部 DNS 域名并按下回车。如果还有其它配置界面出现，继续按下回车以接受默认配置。

#### 2. 下载/安装极狐GitLab

配置极狐GitLab 软件源镜像。

```bash
curl -fsSL https://packages.gitlab.cn/repository/raw/scripts/setup.sh | /bin/bash
```

接下来，安装极狐GitLab。确保您已正确[设置您的 DNS](https://docs.gitlab.cn/omnibus/settings/dns.html)，并更改 https://gitlab.example.com 为您要访问极狐GitLab 实例的 URL。安装包将在该 URL 上自动配置和启动极狐GitLab。

对于 `https` 站点，极狐GitLab 将使用 Let's Encrypt 自动请求 SSL 证书，这需要有效的主机名和入站 HTTP 访问。您也可以使用自己的证书或仅使用 `http://`（不带`s`）。

如果您想为初始管理员用户(`root`)指定自定义密码，请查看[文档](https://docs.gitlab.cn/omnibus/installation/index.html#设置初始密码)。如果未指定密码，将自动生成随机密码。

执行如下命令开始安装：

```bash
sudo EXTERNAL_URL="https://gitlab.example.com" yum install -y gitlab-jh
```

#### 3. 访问极狐GitLab 实例并登录

除非您在安装过程中指定了自定义密码，否则将随机生成一个密码并存储在 /etc/gitlab/initial_root_password 文件中(出于安全原因，24 小时后，此文件会被第一次 `gitlab-ctl reconfigure` 自动删除，因此若使用随机密码登录，建议安装成功初始登录成功之后，立即修改初始密码）。使用此密码和用户名 `root` 登录。

有关安装和配置的详细说明，请参阅我们的[文档](https://docs.gitlab.cn/omnibus/installation/)。

#### 4. 后续配置

完成安装后，请参考建议的[后续配置](https://docs.gitlab.cn/jh/install/next_steps.html)，包括身份验证选项和注册限制的配置。