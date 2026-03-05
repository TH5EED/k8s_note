kubeadm安装
	组件通过容器化方式运行
	简单，自愈

二进制安装
	组件变成系统进程的方式运行
	灵活安装集群，规模可以更大

# 网卡配置
网卡两块：仅主机ens160，nat模式ens192
因为calico，先关闭第二块网卡：autoconnect=false关闭开机自启

# 系统软件源
去/etc/yum.repos.d/中
查看repo文件内容，grep过滤有没有国内镜像网站（aliyun.com)

# 防火墙修改
关闭且禁用firewalld，改用iptables

# 禁用seliunx，设置时区，关闭swap分区
# 主机名修改
/etc/hosts
# 安装ipvs，开启路由转发，加载网桥
# 安装docker
先从中科大镜像站下载官方yum源配置文件，结果是在/etc/yum.repos.d目录下生成docker-ce.repo文件

这个配置文件中的网址很慢，需要替换成国内镜像

然后就可以下载docker-ce

