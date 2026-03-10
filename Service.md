暴露服务的重要方式

负载均衡，一组pod可以被service访问到，需要通过Label Selector（就绪探测后）
# 核心
集群中，每个节点都运行了一个kube-proxy进程，负责为service实现一种VIP（虚拟IP）的形式
默认代理为iptables（防火墙)，新版本增加了ipvs代理




























