# Kubernetes中request的作用


在完成柔性计算项目的过程中，总结request在kubernetes中的各种作用。如有不正确的地方或者有补充的点，欢迎联系我。

## request出现之处

在 Kubernetes 中，Pod 是最小的可调度的部署单元。Pod 包含一个或多个容器，共享相同的网络命名空间和卷存储空间。在 Kubernetes 中，Pod 用来承载应用程序，并提供资源的抽象。Pod 中的 **requests 字段是用来定义容器需要的最小计算资源的，主要包括 CPU 和内存。** requests 字段可以在 Pod 级别或容器级别设置。在 Pod 级别设置 requests 字段可以确保所有容器都具有相同的资源要求。在容器级别设置 requests 字段可以确保每个容器都有特定的资源要求。

## request作用分析

为Pod设置request之后，其会在 Kubernetes 的Scheduler、Kubelet中发挥作用。下面进行详细分析。

### request作用在Scheduler

- 当Pod设置中设置了request

Scheduler在调度Pod到合适节点的过程中，会经过预选和优选两个阶段，其中会有各种各样的检查和评分策略，以为Pod找到最适合它的节点。在**预选**过程中有一个策略叫 *NodeResourcesFit* ，其作用是检查节点是否拥有Pod 请求的所有资源。实现的扩展点有preFilter、filter、score。其中的重点是 filter 的过程。

filter 过程Scheduler是如何实现的呢？它首先获取到集群中所有节点，然后检查节点是由有满足Pod请求的最小资源(既是节点的剩余可用资源是否大于Pod中设置的request)，如果不满足，则过滤掉当前节点。在计算节点的剩余可用资源时，Scheduler统计运行在节点上的每一个Pod的request，并把它们累加，如果 podRequest.MilliCPU > (nodeInfo.Allocatable.MilliCPU - nodeInfo.Requested.MilliCPU)，那么该节点将会被过滤(这里以CPU举例，检查Memory是否满足要求也是同样的方法)。

- 当Pod未设置request

Pod中未设置request时，Scheduler则认为Pod需要的最小计算资源为0，认为此Pod可以运行在任意一个节点上。故此时Scheduler不再对节点进行过滤和评分，而时把Pod调度到随机的节点上。

### request作用在Kubelet

当Pod被调度成功之后，对应节点上的Kubelet会创建、运行、管理这个Pod。Kubelet管理节点上所有Pod的生命周期。当节点上所有Pod的request资源之和大于等于节点的Allocatable资源时，kubelet不创建最后一个被调度到此节点的Pod，并将最后一个被调度到此节点的Pod状态设为 OutOfcpu 或者 OutOfmemory。

 

