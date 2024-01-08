# 修改kubelet配置


  记录在验证kubelet压力驱逐原则时的遇到的问题与解决方法。

## 验证节点压力驱逐原则时，实验结果与官方文档给出的规则相反

  kubernetes的官方文档中给出，当节点内存资源紧张时，会根据以下的**三条**规则驱逐节点上的pod以回收内存资源，缓解内存资源压力：

1. 先根据pod的内存使用量是否超过其request进行排序，超过的排在前面；

2. 再根据pod的priority值大小进行排序，值小的排在前面；

3. 最后根据pod内存request减去pod的实际内存使用量的值，得到值较小的排在前面；

我的集群环境：

- 三台基于Centos7.6操作系统的虚拟机

- kubernetes版本1.23.0

- 未更改kubelet原本的配置文件

### 实验过程

  自定义三个low、mid、high优先级类，在节点node1上部署两个pod，内存资源使用量大的pod而优先级高，内存使用量小的pod而优先级低。待他们运行稳定之后，再部署一个抢占node1内存资源的pod造成节点内存资源不足而触发驱逐，观察kubelet先驱逐哪一个pod。（**注意：**上述两个测试用的pod均没有设置request，故他们的request为0，驱逐的顺序由第2和第3点决定；同时保证抢占内存的pod不会被先驱逐）

### 实验结果

  使用命令 `kubectl get pods -n example -w` 观察pod的状态（实验时上面的pod都在example的namespace下）。最终是优先级高的pod先被驱逐，并且它的状态直接是error，并没有出现evicted的状态。

### 结果分析

  显然上面的实验结果比不符合kubernetes文档给出的规则，在查阅kubelet源码之后也发现上面的实验结果有误。接着去查看node1上kubelet的配置，发现其节点剩余可用内存剩余小于100Mi时才触发驱逐，并且驱逐时pod的最大宽限期是0秒，这可能导致节点内存资源紧张时pod直接被kubelet kill掉，从而pod的状态直接变成error。

### 思路验证

- 修改kubelet配置

  进入到node1，使用命令打开 `vim /var/lib/kubelet/config.yaml` kubelet的配置文件，添加以下字段：

```shell
evictionMaxPodGracePeriod: 60
evictionHard:
  memory.available: "800Mi"
```

上面是设置kubelet驱逐pod时，给pod的最大宽限期是60秒，当节点剩余可用内存小于800Mi时触发驱逐。然后使用命令 `systemctl restart kubelet` 重启节点上的kubelet，重新实验。

### 二次实验与结果

  同样按照一次实验时的实验步骤再次实验，观察哪一个pod先被驱逐。

  这次实验时低优先级且内存使用量较小的pod先被驱逐，得到了正确的实验结果。故确实是因为kubelet默认的产生驱逐的阈值太高而导致出现错误的实验结果，当把kubelet内存的驱逐阈值调低即得到了正确的结果。

