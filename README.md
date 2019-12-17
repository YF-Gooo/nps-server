# 内网穿透实验

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
# 可能会有点延迟
