# 内网穿透实验
https://cnlh.github.io/nps/#/example?id=%e7%a7%81%e5%af%86%e4%bb%a3%e7%90%86
# 启动服务端
scp -r linux_amd64_server/nps root@ip:/opt/
./nps start

# 统一准备工作（必做）

    开启服务端，假设公网服务器ip为1.1.1.1，配置文件中bridge_port为8024，配置文件中web_port为8080
    访问1.1.1.1:8080
    在客户端管理中创建一个客户端(直接点确定就行，不需要填任何东西)，记录下验证密钥vkey
    内网客户端运行（windows使用cmd运行加.exe）
    sudo ./npc -server=1.1.1.1:8024 -vkey=客户端的密钥
# 域名不要在设计成www了，改成*

# 域名解析

适用范围： 小程序开发、微信公众号开发、产品演示

假设场景：

    有一个域名proxy.com，有一台公网机器ip为1.1.1.1
    两个内网开发站点127.0.0.1:81，127.0.0.1:82
    想通过（http|https://）a.proxy.com访问127.0.0.1:81，通过（http|https://）b.proxy.com访问127.0.0.1:82

使用步骤

    将*.proxy.com解析到公网服务器1.1.1.1
    点击刚才创建的客户端的域名管理，添加两条规则规则：1、域名：a.proxy.com，内网目标：127.0.0.1:81，2、域名：b.proxy.com，内网目标：127.0.0.1:82

现在访问（http|https://）a.proxy.com，b.proxy.com即可成功

# tcp隧道
首先现在客户端上安装ssh server

    apt-get install openssh-server
    sudo vim /etc/ssh/sshd_config
    将
    PermitRootLogin prohibit-password
    改为
    PermitRootLogin yes
    sudo service ssh restart

适用范围： ssh、远程桌面等tcp连接场景

假设场景： 想通过访问公网服务器1.1.1.1的8001端口，连接内网机器10.1.50.101的22端口，实现ssh连接

使用步骤

    在刚才创建的客户端隧道管理中添加一条tcp隧道，填写监听的端口（8001）、内网目标ip和目标端口（10.1.50.101:22），保存。
    访问公网服务器ip（1.1.1.1）,填写的监听端口(8001)，相当于访问内网ip(10.1.50.101):目标端口(22)，例如：ssh -p 8001 root@1.1.1.1

# 私密代理

适用范围： 无需占用多余的端口、安全性要求较高可以防止其他人连接的tcp服务，例如ssh。

假设场景： 无需新增多的端口实现访问内网服务器10.1.50.2的22端口

使用步骤

    在刚才创建的客户端中添加一条私密代理，并设置唯一密钥secrettest和内网目标10.1.50.2:22
    在需要连接ssh的机器上以执行命令

./npc -server=1.1.1.1:8024 -vkey=vkey -type=tcp -password=secrettest -local_type=secret

如需指定本地端口可加参数-local_port=xx，默认为2000

注意： password为web管理上添加的唯一密钥，具体命令可查看web管理上的命令提示

假设10.1.50.2用户名为root，现在执行ssh -p 2000 root@1.1.1.1即可访问ssh

# p2p服务

适用范围： 大流量传输场景，流量不经过公网服务器，但是由于p2p穿透和nat类型关系较大，不保证100%成功，支持大部分nat类型。nat类型检测

假设场景：

想通过访问使用端机器（访问端，也就是本机）的2000端口---->访问到内网机器 10.2.50.2的22端口

使用步骤

    在nps.conf中设置p2p_ip（nps服务器ip）和p2p_port（nps服务器udp端口）
    在刚才刚才创建的客户端中添加一条p2p代理，并设置唯一密钥p2pssh
    在使用端机器（本机）执行命令

./npc -server=1.1.1.1:8284 -vkey=123 -password=p2pssh -target=10.2.50.2:22

如需指定本地端口可加参数-local_port=xx，默认为2000

注意： password为web管理上添加的唯一密钥，具体命令可查看web管理上的命令提示

假设内网机器为10.2.50.2的ssh用户名为root，现在在本机上执行ssh -p 2000 root@127.0.0.1即可访问机器2的ssh，如果是网站在浏览器访问127.0.0.1:2000端口即可。

# 可能会有点延迟
