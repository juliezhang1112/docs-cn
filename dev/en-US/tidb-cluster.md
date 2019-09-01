---
title: Kubernetes 上的 TiDB 集群配置
category: reference
---

# Kubernetes 上的 TiDB 集群配置

本文介绍 Kubernetes 上 TiDB 集群的配置参数、资源配置，以及容灾配置。

## 配置参数

TiDB Operator 使用 Helm 部署和管理 TiDB 集群。通过 Helm 获取的配置文件默认提供了基本的配置，通过这个基本配置，可以快速启动一个 TiDB 集群。但是如果用户需要特殊配置或是用于生产环境，则需要根据以下配置参数列表手动配置对应的配置项。

> **注意：**
> 
> 下文用 `values.yaml` 指代要修改的 TiDB 集群配置文件。

<table>
  <tr>
    <th align="left">
      参数名
    </th>
    
    <th align="left">
      说明
    </th>
    
    <th align="left">
      默认值
    </th>
  </tr>
  
  <tr>
    <td align="left">
      <code>rbac.create</code>
    </td>
    
    <td align="left">
      是否启用 Kubernetes 的 RBAC
    </td>
    
    <td align="left">
      <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>clusterName</code>
    </td>
    
    <td align="left">
      TiDB 集群名，默认不设置该变量，<code>tidb-cluster</code> 会直接用执行安装时的 <code>ReleaseName</code> 代替
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>extraLabels</code>
    </td>
    
    <td align="left">
      添加额外的 labels 到 <code>TidbCluster</code> 对象 (CRD) 上，参考：<a href="https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/">labels</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>schedulerName</code>
    </td>
    
    <td align="left">
      TiDB 集群使用的调度器
    </td>
    
    <td align="left">
      <code>tidb-scheduler</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>timezone</code>
    </td>
    
    <td align="left">
      TiDB 集群默认时区
    </td>
    
    <td align="left">
      <code>UTC</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pvReclaimPolicy</code>
    </td>
    
    <td align="left">
      TiDB 集群使用的 PV (Persistent Volume)的 reclaim policy
    </td>
    
    <td align="left">
      <code>Retain</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>services[0].name</code>
    </td>
    
    <td align="left">
      TiDB 集群对外暴露服务的名字
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>services[0].type</code>
    </td>
    
    <td align="left">
      TiDB 集群对外暴露服务的类型，(从 <code>ClusterIP</code>、<code>NodePort</code>、<code>LoadBalancer</code> 中选择)
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.image</code>
    </td>
    
    <td align="left">
      TiDB 集群 PD 服务发现组件的镜像，该组件用于在 PD 集群第一次启动时，为各个 PD 实例提供服务发现功能以协调启动顺序
    </td>
    
    <td align="left">
      <code>pingcap/tidb-operator:v1.0.0-beta.3</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.imagePullPolicy</code>
    </td>
    
    <td align="left">
      PD 服务发现组件镜像的拉取策略
    </td>
    
    <td align="left">
      <code>IfNotPresent</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.resources.limits.cpu</code>
    </td>
    
    <td align="left">
      PD 服务发现组件的 CPU 资源限额
    </td>
    
    <td align="left">
      <code>250m</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.resources.limits.memory</code>
    </td>
    
    <td align="left">
      PD 服务发现组件的内存资源限额
    </td>
    
    <td align="left">
      <code>150Mi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.resources.requests.cpu</code>
    </td>
    
    <td align="left">
      PD 服务发现组件的 CPU 资源请求
    </td>
    
    <td align="left">
      <code>80m</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>discovery.resources.requests.memory</code>
    </td>
    
    <td align="left">
      PD 服务发现组件的内存资源请求
    </td>
    
    <td align="left">
      <code>50Mi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>enableConfigMapRollout</code>
    </td>
    
    <td align="left">
      是否开启 TiDB 集群自动滚动更新。如果启用，则 TiDB 集群的 ConfigMap 变更时，TiDB 集群自动更新对应组件。该配置只在 tidb-operator v1.0 及以上版本才支持
    </td>
    
    <td align="left">
      <code>false</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.config</code>
    </td>
    
    <td align="left">
      配置文件格式的 PD 的配置，请参考<a href="https://github.com/pingcap/pd/blob/master/conf/config.toml">链接</a>查看默认 PD 配置文件（选择对应 PD 版本的 tag），可以参考<a href="/reference/configuration/pd-server/configuration-file.md">文档</a>查看配置参数的具体介绍（请选择对应的文档版本），这里只需要<strong>按照配置文件中的格式修改配置</strong>
    </td>
    
    <td align="left">
      TiDB Operator 版本 <= v1.0.0-beta.3，默认值为：<br /><code>nil</code><br />TiDB Operator 版本 > v1.0.0-beta.3，默认值为：<br /><code>[log]</code><br /><code>level = "info"</code><br /><code>[replication]</code><br /><code>location-labels = ["region", "zone", "rack", "host"]</code><br />配置示例：<br />&nbsp;&nbsp;<code>config:</code> \|<br />&nbsp;&nbsp;&nbsp;&nbsp;<code>[log]</code><br />&nbsp;&nbsp;&nbsp;&nbsp;<code>level = "info"</code><br />&nbsp;&nbsp;&nbsp;&nbsp;<code>[replication]</code><br />&nbsp;&nbsp;&nbsp;&nbsp;<code>location-labels = ["region", "zone", "rack", "host"]</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.replicas</code>
    </td>
    
    <td align="left">
      PD 的 Pod 数
    </td>
    
    <td align="left">
      <code>3</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.image</code>
    </td>
    
    <td align="left">
      PD 镜像
    </td>
    
    <td align="left">
      <code>pingcap/pd:v3.0.0-rc.1</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.imagePullPolicy</code>
    </td>
    
    <td align="left">
      PD 镜像的拉取策略
    </td>
    
    <td align="left">
      <code>IfNotPresent</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.logLevel</code>
    </td>
    
    <td align="left">
      PD 日志级别<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>pd.config</code> 配置：<br /><code>[log]</code><br /><code>level = "info"</code>
    </td>
    
    <td align="left">
      <code>info</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.storageClassName</code>
    </td>
    
    <td align="left">
      PD 使用的 storageClass，storageClassName 指代一种由 Kubernetes 集群提供的存储类型，不同的类可能映射到服务质量级别、备份策略或集群管理员确定的任意策略。详细参考：<a href="https://kubernetes.io/docs/concepts/storage/storage-classes">storage-classes</a>
    </td>
    
    <td align="left">
      <code>local-storage</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.maxStoreDownTime</code>
    </td>
    
    <td align="left">
      <code>pd.maxStoreDownTime</code> 指一个 store 节点断开连接多长时间后状态会被标记为 <code>down</code>，当状态变为 <code>down</code> 后，store 节点开始迁移数据到其它 store 节点<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>pd.config</code> 配置：<br /><code>[schedule]</code><br /><code>max-store-down-time = "30m"</code>
    </td>
    
    <td align="left">
      <code>30m</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.maxReplicas</code>
    </td>
    
    <td align="left">
      <code>pd.maxReplicas</code> 是 TiDB 集群的数据的副本数<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>pd.config</code> 配置：<br /><code>[replication]</code><br /><code>max-replicas = 3</code>
    </td>
    
    <td align="left">
      <code>3</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.limits.cpu</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的 CPU 资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.limits.memory</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的内存资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.limits.storage</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的存储容量限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.requests.cpu</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的 CPU 资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.requests.memory</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的内存资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.resources.requests.storage</code>
    </td>
    
    <td align="left">
      每个 PD Pod 的存储容量请求
    </td>
    
    <td align="left">
      <code>1Gi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.affinity</code>
    </td>
    
    <td align="left">
      <code>pd.affinity</code> 定义 PD 的调度规则和偏好，详细请参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#affinity-and-anti-baffinity">affinity-and-anti-affinity</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.nodeSelector</code>
    </td>
    
    <td align="left">
      <code>pd.nodeSelector</code> 确保 PD Pods 只调度到以该键值对作为标签的节点，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#nodeselector">nodeselector</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.tolerations</code>
    </td>
    
    <td align="left">
      <code>pd.tolerations</code> 应用于 PD Pods，允许 PD Pods 调度到含有指定 taints 的节点上，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/taint-and-toleration">taint-and-toleration</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>pd.annotations</code>
    </td>
    
    <td align="left">
      为 PD Pods 添加特定的 <code>annotations</code>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.config</code>
    </td>
    
    <td align="left">
      配置文件格式的 TiKV 的配置，请参考<a href="https://github.com/tikv/tikv/blob/master/etc/config-template.toml">链接</a>查看默认 TiKV 配置文件（选择对应 TiKV 版本的 tag），可以参考<a href="https://pingcap.com/docs-cn/v3.0/reference/configuration/tikv-server/configuration-file/">文档</a>查看配置参数的具体介绍（请选择对应的文档版本），这里只需要<strong>按照配置文件中的格式修改配置</strong>
    </td>
    
    <td align="left">
      TiDB Operator 版本 <= v1.0.0-beta.3，默认值为：<br /><code>nil</code><br />TiDB Operator 版本 > v1.0.0-beta.3，默认值为：<br /><code>log-level = "info"</code><br />配置示例：<br />&nbsp;&nbsp;<code>config:</code> \|<br />&nbsp;&nbsp;&nbsp;&nbsp;<code>log-level = "info"</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.replicas</code>
    </td>
    
    <td align="left">
      TiKV 的 Pod 数
    </td>
    
    <td align="left">
      <code>3</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.image</code>
    </td>
    
    <td align="left">
      TiKV 的镜像
    </td>
    
    <td align="left">
      <code>pingcap/tikv:v3.0.0-rc.1</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.imagePullPolicy</code>
    </td>
    
    <td align="left">
      TiKV 镜像的拉取策略
    </td>
    
    <td align="left">
      <code>IfNotPresent</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.logLevel</code>
    </td>
    
    <td align="left">
      TiKV 的日志级别<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>log-level = "info"</code>
    </td>
    
    <td align="left">
      <code>info</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.storageClassName</code>
    </td>
    
    <td align="left">
      TiKV 使用的 storageClass，storageClassName 指代一种由 Kubernetes 集群提供的存储类型，不同的类可能映射到服务质量级别、备份策略或集群管理员确定的任意策略。详细参考：<a href="https://kubernetes.io/docs/concepts/storage/storage-classes">storage-classes</a>
    </td>
    
    <td align="left">
      <code>local-storage</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.syncLog</code>
    </td>
    
    <td align="left">
      syncLog 指是否启用 raft 日志同步功能，启用该功能能保证在断电时数据不丢失<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[raftstore]</code><br /><code>sync-log = true</code>
    </td>
    
    <td align="left">
      <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.grpcConcurrency</code>
    </td>
    
    <td align="left">
      配置 gRPC server 线程池大小<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[server]</code><br /><code>grpc-concurrency = 4</code>
    </td>
    
    <td align="left">
      <code>4</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.limits.cpu</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的 CPU 资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.limits.memory</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的内存资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.limits.storage</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的存储容量限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.requests.cpu</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的 CPU 资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.requests.memory</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的内存资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.resources.requests.storage</code>
    </td>
    
    <td align="left">
      每个 TiKV Pod 的存储容量请求
    </td>
    
    <td align="left">
      <code>10Gi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.affinity</code>
    </td>
    
    <td align="left">
      <code>tikv.affinity</code> 定义 TiKV 的调度规则和偏好，详细请参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#affinity-and-anti-baffinity">affinity-and-anti-affinity</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.nodeSelector</code>
    </td>
    
    <td align="left">
      <code>tikv.nodeSelector</code>确保 TiKV Pods 只调度到以该键值对作为标签的节点，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#nodeselector">nodeselector</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.tolerations</code>
    </td>
    
    <td align="left">
      <code>tikv.tolerations</code> 应用于 TiKV Pods，允许 TiKV Pods 调度到含有指定 taints 的节点上，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/taint-and-toleration">taint-and-toleration</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.annotations</code>
    </td>
    
    <td align="left">
      为 TiKV Pods 添加特定的 <code>annotations</code>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.defaultcfBlockCacheSize</code>
    </td>
    
    <td align="left">
      指定 block 缓存大小，block 缓存用于缓存未压缩的 block，较大的 block 缓存设置可以加快读取速度。一般推荐设置为 <code>tikv.resources.limits.memory</code> 的 30%-50%<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[rocksdb.defaultcf]</code><br /><code>block-cache-size = "1GB"</code><br />从 TiKV v3.0.0 开始，不再需要配置 <code>[rocksdb.defaultcf].block-cache-size</code> 和 <code>[rocksdb.writecf].block-cache-size</code>，改为配置 <code>[storage.block-cache].capacity</code>
    </td>
    
    <td align="left">
      <code>1GB</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.writecfBlockCacheSize</code>
    </td>
    
    <td align="left">
      指定 writecf 的 block 缓存大小，一般推荐设置为 <code>tikv.resources.limits.memory</code> 的 10%-30%<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[rocksdb.writecf]</code><br /><code>block-cache-size = "256MB"</code><br />从 TiKV v3.0.0 开始，不再需要配置 <code>[rocksdb.defaultcf].block-cache-size</code> 和 <code>[rocksdb.writecf].block-cache-size</code>，改为配置 <code>[storage.block-cache].capacity</code>
    </td>
    
    <td align="left">
      <code>256MB</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.readpoolStorageConcurrency</code>
    </td>
    
    <td align="left">
      TiKV 存储的高优先级/普通优先级/低优先级操作的线程池大小<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[readpool.storage]</code><br /><code>high-concurrency = 4</code><br /><code>normal-concurrency = 4</code><br /><code>low-concurrency = 4</code>
    </td>
    
    <td align="left">
      <code>4</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.readpoolCoprocessorConcurrency</code>
    </td>
    
    <td align="left">
      一般如果 <code>tikv.resources.limits.cpu</code> > 8，则 <code>tikv.readpoolCoprocessorConcurrency</code> 设置为<code>tikv.resources.limits.cpu</code> * 0.8<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[readpool.coprocessor]</code><br /><code>high-concurrency = 8</code><br /><code>normal-concurrency = 8</code><br /><code>low-concurrency = 8</code>
    </td>
    
    <td align="left">
      <code>8</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tikv.storageSchedulerWorkerPoolSize</code>
    </td>
    
    <td align="left">
      TiKV 调度程序的工作池大小，应在重写情况下增加，同时应小于总 CPU 核心<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tikv.config</code> 配置：<br /><code>[storage]</code><br /><code>scheduler-worker-pool-size = 4</code>
    </td>
    
    <td align="left">
      <code>4</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.config</code>
    </td>
    
    <td align="left">
      配置文件格式的 TiDB 的配置，请参考<a href="https://github.com/pingcap/tidb/blob/master/config/config.toml.example">链接</a>查看默认 TiDB 配置文件（选择对应 TiDB 版本的 tag），可以参考<a href="/reference/configuration/tidb-server/configuration-file.md">文档</a>查看配置参数的具体介绍（请选择对应的文档版本），这里只需要<strong>按照配置文件中的格式修改配置</strong>
    </td>
    
    <td align="left">
      TiDB Operator 版本 <= v1.0.0-beta.3，默认值为：<br /><code>nil</code><br />TiDB Operator 版本 > v1.0.0-beta.3，默认值为：<br /><code>[log]</code><br /><code>level = "info"</code><br />配置示例：<br />&nbsp;&nbsp;<code>config:</code> \|<br />&nbsp;&nbsp;&nbsp;&nbsp;<code>[log]</code><br />&nbsp;&nbsp;&nbsp;&nbsp;<code>level = "info"</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.replicas</code>
    </td>
    
    <td align="left">
      TiDB 的 Pod 数
    </td>
    
    <td align="left">
      <code>2</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.image</code>
    </td>
    
    <td align="left">
      TiDB 的镜像
    </td>
    
    <td align="left">
      <code>pingcap/tidb:v3.0.0-rc.1</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.imagePullPolicy</code>
    </td>
    
    <td align="left">
      TiDB 镜像的拉取策略
    </td>
    
    <td align="left">
      <code>IfNotPresent</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.logLevel</code>
    </td>
    
    <td align="left">
      TiDB 的日志级别<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[log]</code><br /><code>level = "info"</code>
    </td>
    
    <td align="left">
      <code>info</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.resources.limits.cpu</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 CPU 资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.resources.limits.memory</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的内存资源限额
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.resources.requests.cpu</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 CPU 资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.resources.requests.memory</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的内存资源请求
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.passwordSecretName</code>
    </td>
    
    <td align="left">
      存放 TiDB 用户名及密码的 Secret 的名字，该 Secret 可以使用以下命令创建机密：<code>kubectl create secret generic tidb secret--from literal=root=&lt;root password&gt;--namespace=&lt;namespace&gt;</code>，如果没有设置，则 TiDB 根密码为空
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.initSql</code>
    </td>
    
    <td align="left">
      在 TiDB 集群启动成功后，会执行的初始化脚本
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.affinity</code>
    </td>
    
    <td align="left">
      <code>tidb.affinity</code> 定义 TiDB 的调度规则和偏好，详细请参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#affinity-and-anti-baffinity">affinity-and-anti-affinity</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.nodeSelector</code>
    </td>
    
    <td align="left">
      <code>tidb.nodeSelector</code>确保 TiDB Pods 只调度到以该键值对作为标签的节点，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#nodeselector">nodeselector</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.tolerations</code>
    </td>
    
    <td align="left">
      <code>tidb.tolerations</code> 应用于 TiDB Pods，允许 TiDB Pods 调度到含有指定 taints 的节点上，详情参考：<a href="https://kubernetes.io/docs/concepts/configuration/taint-and-toleration">taint-and-toleration</a>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.annotations</code>
    </td>
    
    <td align="left">
      为 TiDB Pods 添加特定的 <code>annotations</code>
    </td>
    
    <td align="left">
      <code>{}</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.maxFailoverCount</code>
    </td>
    
    <td align="left">
      TiDB 最大的故障转移数量，假设为 3 即最多支持同时 3 个 TiDB 实例故障转移
    </td>
    
    <td align="left">
      <code>3</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.type</code>
    </td>
    
    <td align="left">
      TiDB 服务对外暴露类型
    </td>
    
    <td align="left">
      <code>NodePort</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.externalTrafficPolicy</code>
    </td>
    
    <td align="left">
      表示此服务是否希望将外部流量路由到节点本地或集群范围的端点。有两个可用选项：<code>Cluster</code>（默认）和 <code>Local</code>。<code>Cluster</code> 隐藏了客户端源 IP，可能导致流量需要二次跳转到另一个节点，但具有良好的整体负载分布。<code>Local</code> 保留客户端源 IP 并避免 LoadBalancer 和 NodePort 类型服务流量的第二次跳转，但存在潜在的不均衡流量传播风险。详细参考：<a href="https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#preserving-the-client-source-ip">外部负载均衡器</a>
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.loadBalancerIP</code>
    </td>
    
    <td align="left">
      指定 tidb 负载均衡 IP，某些云提供程序允许您指定loadBalancerIP。在这些情况下，将使用用户指定的loadBalancerIP创建负载平衡器。如果未指定loadBalancerIP字段，则将使用临时IP地址设置loadBalancer。如果指定loadBalancerIP但云提供程序不支持该功能，则将忽略您设置的loadbalancerIP字段
    </td>
    
    <td align="left">
      <code>nil</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.mysqlNodePort</code>
    </td>
    
    <td align="left">
      TiDB 服务暴露的 mysql NodePort 端口
    </td>
    
    <td align="left">
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.exposeStatus</code>
    </td>
    
    <td align="left">
      TiDB 服务是否暴露状态端口
    </td>
    
    <td align="left">
      <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.service.statusNodePort</code>
    </td>
    
    <td align="left">
      指定 TiDB 服务的状态端口暴露的 <code>NodePort</code>
    </td>
    
    <td align="left">
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.separateSlowLog</code>
    </td>
    
    <td align="left">
      是否以 sidecar 方式运行独立容器输出 TiDB 的 SlowLog
    </td>
    
    <td align="left">
      如果 TiDB Operator 版本 <= v1.0.0-beta.3，默认值为 <code>false</code><br />如果 TiDB Operator 版本 > v1.0.0-beta.3，默认值为 <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.slowLogTailer.image</code>
    </td>
    
    <td align="left">
      TiDB 的 slowLogTailer 的镜像，slowLogTailer 是一个 sidecar 类型的容器，用于输出 TiDB 的 SlowLog，该配置仅在 <code>tidb.separateSlowLog</code>=<code>true</code> 时生效
    </td>
    
    <td align="left">
      <code>busybox:1.26.2</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.slowLogTailer.resources.limits.cpu</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 slowLogTailer 的 CPU 资源限额
    </td>
    
    <td align="left">
      <code>100m</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.slowLogTailer.resources.limits.memory</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 slowLogTailer 的内存资源限额
    </td>
    
    <td align="left">
      <code>50Mi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.slowLogTailer.resources.requests.cpu</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 slowLogTailer 的 CPU 资源请求
    </td>
    
    <td align="left">
      <code>20m</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.slowLogTailer.resources.requests.memory</code>
    </td>
    
    <td align="left">
      每个 TiDB Pod 的 slowLogTailer 的内存资源请求
    </td>
    
    <td align="left">
      <code>5Mi</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.plugin.enable</code>
    </td>
    
    <td align="left">
      是否启用 TiDB 插件功能
    </td>
    
    <td align="left">
      <code>false</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.plugin.directory</code>
    </td>
    
    <td align="left">
      指定 TiDB 插件所在的目录
    </td>
    
    <td align="left">
      <code>/plugins</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.plugin.list</code>
    </td>
    
    <td align="left">
      指定 TiDB 加载的插件列表，plugin ID 命名规则：插件名-版本，例如：'conn_limit-1'
    </td>
    
    <td align="left">
      <code>[]</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.preparedPlanCacheEnabled</code>
    </td>
    
    <td align="left">
      是否启用 TiDB 的 prepared plan 缓存<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[prepared-plan-cache]</code><br /><code>enabled = false</code>
    </td>
    
    <td align="left">
      <code>false</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.preparedPlanCacheCapacity</code>
    </td>
    
    <td align="left">
      TiDB 的 prepared plan 缓存数量<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[prepared-plan-cache]</code><br /><code>capacity = 100</code>
    </td>
    
    <td align="left">
      <code>100</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.txnLocalLatchesEnabled</code>
    </td>
    
    <td align="left">
      是否启用事务内存锁，当本地事务冲突比较多时建议开启<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[txn-local-latches]</code><br /><code>enabled = false</code>
    </td>
    
    <td align="left">
      <code>false</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.txnLocalLatchesCapacity</code>
    </td>
    
    <td align="left">
      事务内存锁的容量，Hash 对应的 slot 数，会自动向上调整为 2 的指数倍。每个 slot 占 32 Bytes 内存。当写入数据的范围比较广时（如导数据），设置过小会导致变慢，性能下降。<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[txn-local-latches]</code><br /><code>capacity = 10240000</code>
    </td>
    
    <td align="left">
      <code>10240000</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.tokenLimit</code>
    </td>
    
    <td align="left">
      TiDB 并发执行会话的限制<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>token-limit = 1000</code>
    </td>
    
    <td align="left">
      <code>1000</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.memQuotaQuery</code>
    </td>
    
    <td align="left">
      TiDB 查询的内存限额，默认 32GB<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>mem-quota-query = 34359738368</code>
    </td>
    
    <td align="left">
      <code>34359738368</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.txnEntryCountLimit</code>
    </td>
    
    <td align="left">
      一个事务中条目的数目限制。如果使用 TiKV 作为存储，则条目表示键/值对。<strong>警告</strong>：不要将该值设置得太大，否则会对 TiKV 集群造成很大影响。请仔细调整此配置<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[performance]</code><br /><code>txn-entry-count-limit = 300000</code>
    </td>
    
    <td align="left">
      <code>300000</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.txnTotalSizeLimit</code>
    </td>
    
    <td align="left">
      一个事务中各条目的字节大小限制。如果使用 TiKV 作为存储，则条目表示键/值对。<strong>警告</strong>：不要将该值设置得太大，否则会对 TiKV 集群造成很大影响。请仔细调整此配置<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[performance]</code><br /><code>txn-total-size-limit = 104857600</code>
    </td>
    
    <td align="left">
      <code>104857600</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.enableBatchDml</code>
    </td>
    
    <td align="left">
      <code>tidb.enableBatchDml</code> 为 DML 启用批提交<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>enable-batch-dml = false</code>
    </td>
    
    <td align="left">
      <code>false</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.checkMb4ValueInUtf8</code>
    </td>
    
    <td align="left">
      用于控制当字符集为 utf8 时是否检查 mb4 字符<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>check-mb4-value-in-utf8 = true</code>
    </td>
    
    <td align="left">
      <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.treatOldVersionUtf8AsUtf8mb4</code>
    </td>
    
    <td align="left">
      用于升级兼容性。设置为 <code>true</code> 将把旧版本的表/列的 <code>utf8</code> 字符集视为 <code>utf8mb4</code> 字符集<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>treat-old-version-utf8-as-utf8mb4 = true</code>
    </td>
    
    <td align="left">
      <code>true</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.lease</code>
    </td>
    
    <td align="left">
      <code>tidb.lease</code>是 TiDB Schema lease 的期限，对其更改是非常危险的，除非你明确知道可能产生的结果，否则不建议更改。<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>lease = "45s"</code>
    </td>
    
    <td align="left">
      <code>45s</code>
    </td>
  </tr>
  
  <tr>
    <td align="left">
      <code>tidb.maxProcs</code>
    </td>
    
    <td align="left">
      最大可使用的 CPU 核数，0 代表机器/Pod 上的 CPU 数量<br />如果 TiDB Operator 版本 > v1.0.0-beta.3，请通过 <code>tidb.config</code> 配置：<br /><code>[performance]</code><br /><code>max-procs = 0</code>
    </td>
    
    <td align="left">
      <code>0</code>
    </td>
  </tr>
</table>

## 资源配置说明

部署前需要根据实际情况和需求，为 TiDB 集群各个组件配置资源，其中 PD、TiKV、TiDB 是 TiDB 集群的核心服务组件，在生产环境下它们的资源配置还需要按组件要求指定，具体参考：[资源配置推荐](/how-to/deploy/hardware-recommendations.md)。

为了保证 TiDB 集群的组件在 Kubernetes 中合理的调度和稳定的运行，建议为其设置 Guaranteed 级别的 QoS，通过在配置资源时让 limits 等于 requests 来实现, 具体参考：[配置 QoS](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/)。

如果使用 NUMA 架构的 CPU，为了获得更好的性能，需要在节点上开启 `Static` 的 CPU 管理策略。为了 TiDB 集群组件能独占相应的 CPU 资源，除了为其设置上述 Guaranteed 级别的 QoS 外，还需要保证 CPU 的配额必须是大于或等于 1 的整数。具体参考: [CPU 管理策略](https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies)。

## 容灾配置说明

TiDB 是分布式数据库，它的容灾需要做到在任一个物理拓扑节点发生故障时，不仅服务不受影响，还要保证数据也是完整和可用。下面分别具体说明这两种容灾的配置。

### TiDB 服务的容灾

TiDB 服务容灾本质上基于 Kubernetes 的调度功能来实现的，为了优化调度，TiDB Operator 提供了自定义的调度器，该调度器通过指定的调度算法能在 host 层面，保证 TiDB 服务的容灾，而且目前 TiDB Cluster 使用该调度器作为默认调度器，设置项是上述列表中的 `schedulerName` 配置项。

其它层面的容灾（例如 rack，zone，region）是通过 Affinity 的 `PodAntiAffinity` 来保证，通过 `PodAntiAffinity` 能尽量避免同一组件的不同实例部署到同一个物理拓扑节点上，从而达到容灾的目的，Affinity 的使用参考：[Affinity & AntiAffinity](https://kubernetes.io/docs/concepts/configuration/assign-Pod-node/#affinity-and-anti-baffinity) 。

下面是一个典型的容灾设置例子：

{{< copyable "shell-regular" >}}

```shell
affinity:
 podAntiAffinity:
   preferredDuringSchedulingIgnoredDuringExecution:
   # this term works when the nodes have the label named region
   - weight: 10
     podAffinityTerm:
       labelSelector:
         matchLabels:
           app.kubernetes.io/instance: <release name>
           app.kubernetes.io/component: "pd"
       topologyKey: "region"
       namespaces:
       - <helm namespace>
   # this term works when the nodes have the label named zone
   - weight: 20
     podAffinityTerm:
       labelSelector:
         matchLabels:
           app.kubernetes.io/instance: <release name>
           app.kubernetes.io/component: "pd"
       topologyKey: "zone"
       namespaces:
       - <helm namespace>
   # this term works when the nodes have the label named rack
   - weight: 40
     podAffinityTerm:
       labelSelector:
         matchLabels:
           app.kubernetes.io/instance: <release name>
           app.kubernetes.io/component: "pd"
       topologyKey: "rack"
       namespaces:
       - <helm namespace>
   # this term works when the nodes have the label named kubernetes.io/hostname
   - weight: 80
     podAffinityTerm:
       labelSelector:
         matchLabels:
           app.kubernetes.io/instance: <release name>
           app.kubernetes.io/component: "pd"
       topologyKey: "kubernetes.io/hostname"
       namespaces:
       - <helm namespace>
```

### 数据的容灾

在开始数据容灾配置前，首先请阅读[集群拓扑信息配置](/how-to/deploy/geographic-redundancy/location-awareness.md)。该文档描述了 TiDB 集群数据容灾的实现原理。

在 Kubernetes 上支持数据容灾的功能，需要如下操作：

* 为 PD 设置拓扑位置 Label 集合
    
    > **注意：**
    > 
    > 除 `kubernetes.io/hostname` 外，目前 PD 暂不支持名字中带 `/` 的 Label。
    
    用 Kubernetes 集群 Node 节点上描述拓扑位置的 Label 集合替换 `pd.config` 配置项中里的 `location-labels` 信息。

* 为 TiKV 节点设置所在的 Node 节点的拓扑信息
    
    TiDB Operator 会自动为 TiKV 获取其所在 Node 节点的拓扑信息，并调用 PD 接口将这些信息设置为 TiKV 的 store labels 信息，这样 TiDB 集群就能基于这些信息来调度数据副本。
    
    如果当前 Kubernetes 集群的 Node 节点没有表示拓扑位置的 Label，或者已有的拓扑 Label 名字中带有 `/`，可以通过下面的命令手动给 Node 增加标签：
    
    {{< copyable "shell-regular" >}}
    
        shell
          kubectl label node <nodeName> region=<regionName> zone=<zoneName> rack=<rackName> kubernetes.io/hostname=<hostName>
    
    其中 `region`、`zone`、`rack`、`kubernetes.io/hostname` 只是举例，要添加的 Label 名字和数量可以任意定义，只要符合规范且和 `pd.config` 里的 `location-labels` 设置的 Labels 保持一致即可。