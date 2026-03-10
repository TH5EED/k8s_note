暴露服务的重要方式

负载均衡，一组pod可以被service访问到，需要通过Label Selector（就绪探测后）
# 核心
集群中，每个节点都运行了一个kube-proxy进程，负责通过iptables或ipvs模式为service实现一种VIP（虚拟IP）的形式

kube-proxy是一个网络代理组件

默认代理为iptables（防火墙)，新版本增加了ipvs代理，这是kube-proxy的两种工作模式，决定了如何实现流量转发（通过不同规则）

## iptables
![](assets/Service/file-20260310222529174.png)
不同于userspace，==kube-proxy的工作只监听apiserver，将service变化修改本地的iptables规则。==不再代理当前节点pod的用户请求

监听apiserver：获取当前负载均衡的信息（几台pod

优点：相对于userspace，kube-proxy的功能解耦，压力减小

## ipvs






















