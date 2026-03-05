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
查看repo文件内容 /etc/yum.repos.d/



