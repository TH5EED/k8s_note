暴露服务的重要方式

负载均衡，一组pod可以被service访问到，需要通过Label Selector（就绪探测后）
# 核心
集群中，每个节点都运行了一个kube-proxy进程，负责通过iptables或ipvs模式为service实现一种VIP（虚拟IP）的形式

kube-proxy是一个网络代理组件

默认代理为iptables（防火墙)，新版本增加了ipvs代理，这是kube-proxy的两种工作模式，决定了如何实现流量转发（通过不同规则）

==监听apiserver：获取当前负载均衡的信息==
	==service定义：虚拟IP，端口，协议==
	==endpoints：后端pod的IP，端口==
==随后将这些信息转化为规则==
## iptables工作模式
![](assets/Service/file-20260310222529174.png)
不同于userspace，==kube-proxy的工作只监听apiserver，将service变化修改本地的iptables规则。==不再代理当前节点pod的用户请求

优点：相对于userspace，kube-proxy的功能解耦，压力减小

## ipvs工作模式
同上模式，规则变为ipvs规则，四层性能更强，但很多云厂商阉割这个模块

### 修改kube-proxy模式

```bash
kubectl edit configmap kube-proxy -n kube-system
# -n namespace名字空间，需要指明不然默认default
```

