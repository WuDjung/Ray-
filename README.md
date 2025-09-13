# Ray-Cross-Reg-Container-Cluster-Setup-Notes  
&gt; 「笔记本-云-宿舍」三段网络，一条 Tailscale 内网，7×24 容器级自愈 Ray 集群搭建实录

---

## 1. 技术亮点（TL;DR）
- **零公网 IP**：纯 Tailscale 内网穿透，跨城、跨校区即插即用  
- **容器自愈**：raylet 僵尸 10 秒内自动重启，隔夜不断联  
- **单命令上线**：Docker + shell 脚本，3 分钟从 0 到 2 nodes  
- **可断点续传**：保存/加载镜像，离线也能分发环境  

---

## 2. 最终架构图

```mermaid
mindmap
  root((Ray 集群))
    Head(云机<tailscale ip>)
    Worker1(旧机<tailscale ip>)
    Worker2(树莓派<tailscale ip>)
    Dev(笔记本<tailscale ip>)

mindmap
  root((Ray集群怎么工作))
    头节点
      启动GCS(全局通讯录)
      启动Dashboard(看面板)
      启动Raylet(本地调度员)
    工人节点
      连GCS(报到)
      启动Raylet(等活干)
    Driver(你的Python)
      ray.init(拿地址本)
      提交任务(函数/类)
      拿结果(像本地一样)
    任务流程
      函数变任务
      GCS分给空闲Raylet
      Raylet开Worker进程
      结果写对象存储
      Driver读结果
    Tailscale内网
      像一条宿舍网线
      永不靠公网IP
