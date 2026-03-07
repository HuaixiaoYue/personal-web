---
title: 'n8n科研论文监控方案'
description: '本教程旨在帮助你搭建一个 7x24 小时无人值守 的 AI 代理系统，自动追踪医学影像与人工智能（CV/BME）领域的前沿论文。'
pubDate: '2026-02-10'

---

# 🚀 全栈科研情报自动化系统：从服务器配置到 AI 工作流

---

## 第一阶段：服务器选购与底层环境搭建

### 1. 服务器选购建议

- **地域选择**：强烈建议选择 **中国香港** 或 **美国（如洛杉矶）** 的 VPS（如 RackNerd）。海外服务器能直连 arXiv、PubMed 以及 OpenAI API，无需配置复杂的代理，且部署 Docker 镜像时不会遇到国内镜像源失效的问题。
- **硬件配置**：
  - **内存 (RAM)**： **2GB 起步**（n8n 在处理数据和调用 AI 时较吃内存）。
  - **系统**：首选 **Ubuntu 22.04 LTS** 或 **24.04 LTS**。

### 2. 初始化安全设置

连接服务器（`ssh root@你的服务器IP`）后，先进行基础维护：

```bash
# 1. 修改 root 初始密码（增加安全性）
passwd  

# 2. 更新系统软件包索引并升级
apt update && apt upgrade -y
# 注释：apt update 像“刷新菜单”，apt upgrade 是“正式更新软件”。
# 如果弹出蓝色窗口询问 sshd_config，请选择 "keep the local version currently installed" 以防断连。

# 3. 重启服务器（应用内核更新）
reboot
```

### 3. 安装 Docker 引擎

```bash
# 使用官方一键脚本安装
curl -fsSL https://get.docker.com | bash -s docker

# 启动并设置开机自启
systemctl start docker
systemctl enable docker

# 验证版本（确保显示 Docker version 2x.x.x）
docker --version
```

---

## 第二阶段：n8n 服务深度部署

### 1. 解决权限与持久化问题

n8n 默认使用 `node` 用户（UID 1000）运行。如果不手动修改宿主机文件夹权限，会报 `EACCES: permission denied` 错误。

```bash
# 创建配置存储文件夹
mkdir ~/.n8n

# 将文件夹所有权移交给 n8n 容器内的用户（UID 1000）
chown -R 1000:1000 ~/.n8n
```

### 2. 启动 n8n 容器

```bash
docker run -d \
  --restart always \
  --name n8n \
  -p 5678:5678 \
  -e N8N_SECURE_COOKIE=false \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

- **参数详解**：
  - `-e N8N_SECURE_COOKIE=false`：**非常重要**。如果你使用 HTTP 访问（没有 SSL 证书）或使用 Safari 浏览器，必须设为 false，否则会无法加载界面。
  - `-v ~/.n8n:/home/node/.n8n`：数据卷映射。确保你的工作流保存在服务器硬盘上，重启容器数据不丢失。

### 3. 防火墙放行

```bash
# 允许外部访问 n8n 的 5678 端口
ufw allow 5678/tcp
```

访问：`http://服务器IP:5678`。进入后首先设置管理员账号并领取 **社区版永久免费激活码** 以解锁高级调试功能。

---

## 第三阶段：Notion 数据库集成 (仓库准备)

### 1. 创建 Notion 集成 (Integration)

1. 访问 [Notion Developers](https://www.notion.so/my-integrations) 并点击 **New integration**。
2. **关联工作区**：必须选择你数据库所在的同一个工作区。
3. 获取 **Internal Integration Token** (以 `ntn_` 开头)。

### 2. 数据库配置与授权

1. 在 Notion 页面输入 `/database` 创建一个数据库，并添加列：`Name` (Title), `URL` (URL), `Date` (Date), `AI Summary` (Text)。
2. **关键授权**：点击页面右上角 `...` -> **连接至 (Connect to)** -> 搜索并添加你刚才创建的集成。
3. **获取 ID**：复制数据库链接，URL 中 `/` 之后到 `?` 之前的 32 位随机字符即为 **Database ID**。

---

## 第四阶段：核心工作流逻辑详解 (科研情报 2.0)

### 1. 数据抓取 (HTTP Request)

使用 PubMed API 搜索 CV 或医学影像关键词：

- **URL**：`https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=(Medical+Imaging+OR+MRI+OR+CT)+AND+(Deep+Learning)&reldate=1&datetype=edat&retmode=json`。
- **注释**：PubMed 需要先拿 ID 列表，再通过第二个请求拿标题详情。

### 2. 防重复推送机制 (守门员逻辑)

*注：本节内容仍在优化，读者可自己查阅资料配置*

为了节省 AI Token 并防止 Notion 变乱，必须在 AI 节点前加入查重：

1. **Notion 节点 (Search)**：根据 URL 查询现有数据库。设置 `Always Output Data = On`。
2. **Filter 节点**：
  - **条件**：`id` (来自 Notion 查询节点) **Is Empty**。
  - **逻辑**：如果数据库没查到该 ID，说明是新论文，走 **True** 分支进入 AI 总结。

### 3. AI 智能解析 (Basic LLM Chain)

- **配置**：如果你用第三方 API，需在 **OpenAI Chat Model** 节点里添加 `Base URL` (如 `https://api.deepseek.com/v1`) 并关闭 `Use Responses API` 选项。
- **Prompt 策略**：要求 AI 按照“核心创新点”、“技术路线”和“推荐指数”进行结构化总结。

---

## 第五阶段：系统优化与维护

在 n8n 的 **Workflow Settings** 中：

- **Timezone**：改为 `Asia/Shanghai`，确保定时任务准点。
- **Save Manual Executions**：开启，方便调试时看历史记录。
- **Timeout Workflow**：开启并设为 300 秒，防止死循环卡死服务器。

