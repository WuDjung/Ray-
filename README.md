# Ray-Cross-Reg-Container-Cluster-Setup-Notes  
> 基于「笔记本-云服务器-宿舍设备」三段网络，通过 Tailscale 构建内网，实现7×24小时容器级自愈的 Ray 集群搭建全记录


## 1. 核心技术亮点  
- **零公网依赖**：纯 Tailscale 内网穿透，跨城市/校区设备即插即用，无需公网 IP  
- **自动自愈能力**：raylet 进程异常时10秒内自动重启，保障集群隔夜稳定运行  
- **极速部署**：Docker + 脚本化操作，3分钟内完成从0到2节点的集群搭建  
- **离线可用**：支持镜像保存与加载，无网络环境下也能快速分发运行环境  


## 2. 集群架构与工作原理  

### 2.1 集群节点构成  
```mermaid
mindmap
  root((Ray 集群节点))
    Head(云服务器<br/><tailscale ip>)
    Worker1(旧笔记本<br/><tailscale ip>)
    Worker2(树莓派设备<br/><tailscale ip>)
    Dev(开发笔记本<br/><tailscale ip>)
```

### 2.2 集群工作流程  
```mermaid
mindmap
  root((Ray 集群工作原理))
    头节点(Head Node)
      启动GCS(全局状态服务/通讯录)
      启动Dashboard(集群监控面板)
      启动Raylet(本地任务调度器)
    工作节点(Worker Node)
      连接GCS(注册节点信息)
      启动Raylet(等待任务分配)
    驱动程序(Driver - 你的Python代码)
      ray.init(获取集群地址信息)
      提交任务(远程函数/ Actor类)
      获取结果(接口与本地调用一致)
    任务执行流程
      函数封装为任务
      GCS分配至空闲Raylet
      Raylet启动Worker进程执行
      结果写入分布式对象存储
      Driver从对象存储读取结果
    网络基础(Tailscale内网)
      模拟本地局域网环境
      全程无需暴露公网IP
```
