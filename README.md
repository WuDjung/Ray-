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
    Head(云机&lt;br&gt;100.120.43.113)
    Worker1(南平旧机&lt;br&gt;100.110.69.39)
    Worker2(树莓派&lt;br&gt;100.x.x.x)
    Dev(笔记本&lt;br&gt;100.75.40.95)
