kubeadm安装
	组件通过容器化方式运行
	简单，自愈

二进制安装
	组件变成系统进程的方式运行
	灵活安装集群，规模可以更大

# 网卡配置
网卡两块：仅主机ens160，nat模式ens192
因为calico，先关闭第二块网卡：autoconnect=false关闭开机自启
🛠️ 手把手：用 Windows 网络共享实现仅主机上网（5 分钟搞定）
第一步：确认 VMware 网络设置
打开 VMware Workstation
菜单栏：编辑 > 虚拟网络编辑器
选中 VMnet1（仅主机模式）
记下子网 IP，比如：192.168.183.0
取消勾选 “使用本地 DHCP”（因为我们用 Windows 共享来分配 IP）
⚠️ 如果你不取消，可能会和共享冲突
第二步：在 Windows 上开启网络共享
打开 控制面板 > 网络和 Internet > 网络连接
找到你正在上网的物理网卡（比如 “WLAN” 或 “以太网”）
右键 → 属性
切换到 “共享” 选项卡
勾选：
✅ 允许其他网络用户通过此计算机的 Internet 连接来连接
在下拉菜单中选择：VMnet1
点击 确定
🔄 Windows 会自动把 VMnet1 的 IP 改为 192.168.137.1（这是固定行为）
第三步：调整虚拟机 IP 段（关键！）
因为 Windows 共享后，VMnet1 的网关固定是 192.168.137.1，所以你要：
修改 VMware 虚拟网络（让网段匹配）
回到 虚拟网络编辑器
选中 VMnet1
将子网 IP 改为：192.168.137.0
应用
修改三台 Rocky 虚拟机的 IP
将每台机器的 ens160 改为：
IP：192.168.137.11、.12、.13
子网掩码：255.255.255.0
网关：192.168.137.1
DNS：8.8.8.8
bash

编辑



# 示例：修改 ens160
sudo nmcli con modify ens160 \
    ipv4.addresses 192.168.137.11/24 \
    ipv4.gateway 192.168.137.1 \
    ipv4.dns "8.8.8.8" \
    ipv4.method manual

sudo nmcli con up ens160
第四步：禁用或删除 ens192（可选）
既然你只用仅主机模式，可以把 ens192（NAT 网卡）从虚拟机设置中移除，避免干扰。
✅ 验证是否成功
在 Rocky 上执行：
bash

编辑



ping -c 4 8.8.8.8          # 应通
ping -c 4 www.baidu.com    # 应通
💡 Windows 共享会自动提供：
网关：192.168.137.1
DHCP（可选，但你用静态 IP 也行）
NAT 转发（自动放行所有流量，包括 UDP 53）
❓ 常见问题
Q：为什么 VMnet1 会被改成 192.168.137.0？
A：这是 Windows Internet 连接共享（ICS）的硬编码行为，无法更改。所以你必须配合它。
Q：我能保留 192.168.183.0 网段吗？
A：不能。Windows ICS 强制使用 192.168.137.0/24。如果你坚持用 .183，就必须用软路由。
Q：共享后会影响我本机上网吗？
A：不会。只是多了一个虚拟网卡（VMnet1），你的主网络不受影响。
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
# 配置daemon

