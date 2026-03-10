暴露服务的重要方式

负载均衡，一组pod可以被service访问到，需要通过Label Selector（就绪探测后）
# 核心原理
集群中，每个节点都运行了一个kube-proxy进程，负责通过iptables或ipvs模式为service实现一种VIP（虚拟IP）的形式

kube-proxy是一个网络代理组件

默认代理为iptables（防火墙)，新版本增加了ipvs代理，这是kube-proxy的两种工作模式，决定了如何实现流量转发（通过不同规则）

==监听apiserver：获取当前负载均衡的信息==
	==service定义：虚拟IP，端口，协议==
	==endpoints：后端pod的IP，端口==
==随后将这些信息转化为规则==
## iptables
![](assets/Service/file-20260310222529174.png)
不同于userspace，==kube-proxy的工作只监听apiserver，将service变化修改本地的iptables规则。==不再代理当前节点pod的用户请求

优点：相对于userspace，kube-proxy的功能解耦，压力减小

## ipvs
同上模式，规则变为ipvs规则，四层性能更强，但很多云厂商阉割这个模块
```bash
kubectl edit configmap kube-proxy -n kube-system
# 修改kube-proxy模式
# -n namespace名字空间，需要指明
```

```bash
kubectl delete pod -n kube-system -l k8s-app=kube-proxy
# 修改模式后需要将原来资源删除，自动重建
# 之所以带名字空间，匹配k8s-app，是因为并不是删除集群所有pod，kube-proxy只是组件之一，只是为了更新设置，所以重启它的名字空间下的对应标签之下的pod资源即可
```

```bash
ipvsadm -Ln
# 查看负载均衡，可以看到svc和pod的IP地址
```

# 工作模式
## ClusterIP
默认类型，自动分配一个仅cluster内部可以访问的VIP
![](assets/Service/file-20260310234039544.png)

## NodePort


## LoadBalancer


## ExternalName