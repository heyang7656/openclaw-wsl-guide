# OpenClaw 安装与使用教程（2026 最新版）

本教程提供 OpenClaw 的最新安装方法、配置指南和使用教程，帮助你在 WSL 环境中快速部署这个强大的 AI 智能体平台。

## 前置条件

在开始安装之前，请确保：

- **操作系统**：WSL 2 + Ubuntu 20.04 LTS 或更高版本（推荐 22.04 LTS）
- **Node.js**：版本 ≥ 22.0.0（必须，低于此版本会报错）
- **内存**：至少 2GB 可用内存（推荐 4GB+）
- **磁盘空间**：至少 10GB 可用空间
- **网络连接**：稳定的网络连接（国内用户建议准备代理）
- **API Key**：已准备好 AI 模型的 API 密钥
- **前置工具**：已完成 Opencode 的安装

**预计安装时间**：约 10-15 分钟

---

## 目录

- [一、OpenClaw 简介](#一 openclaw-简介)
- [二、安装 OpenClaw](#二安装-openclaw)
- [三、配置与初始化](#三配置与初始化)
- [四、使用教程](#四使用教程)
- [五、国内 API 配置](#五国内-api-配置)
- [六、常见问题 A&Q](#六常见问题-aq)
- [七、卸载指南](#七卸载指南)
- [八、性能调优建议](#八性能调优建议)

---

## 一、OpenClaw 简介

### 什么是 OpenClaw？

**OpenClaw**（曾用名 Clawdbot/Moltbot）是一个开源、免费、本地优先的 AI 自动化代理平台。它不仅仅是聊天机器人，而是能**真正执行任务**的 AI 助手。

### 核心特点

#### 1. 真正的行动能力
- **传统 AI**：你问"帮我整理桌面文件"，它只会给你文字建议
- **OpenClaw**：你下达指令，它直接重命名、分类、移动文件，并告诉你"已完成"

#### 2. 多平台接入
支持通过各种消息平台与智能体互动：
- WhatsApp / Telegram / Discord / iMessage
- 飞书 / 企业微信 / QQ / 微信
- Web UI / 本地终端

#### 3. 技能扩展系统
通过"Skills（技能）"系统扩展能力：
- 文件操作技能
- 浏览器控制技能
- 搜索技能
- 命令执行技能
- 第三方 API 集成

#### 4. 本地部署与隐私控制
- 在用户自己的机器上运行
- 数据不用上传到第三方服务器
- 完全掌控自己的数据和配置

#### 5. 灵活的模型支持
支持几乎所有主流大模型：
- GPT-4 / GPT-3.5
- Claude 系列
- Google Gemini
- MiniMax（国内推荐）
- 智谱 GLM（国内推荐）
- Ollama 本地模型（完全免费）

### 应用场景

#### 日常办公
- 自动整理文件、重命名、分类
- 自动查资料、总结新闻、写报告
- 自动发消息、提醒、安排日程

#### 自动化任务
- 网页自动搜索、自动填表、自动抓取信息
- 自动监控数据、监控新闻
- 自动执行脚本、跑命令

#### 开发辅助
- 写代码、改 BUG、解释代码
- 自动部署、自动检查项目
- 代码审查和优化建议

---

## 二、安装 OpenClaw

### 2.1 环境准备

**步骤 1**：检查并安装 Node.js 22

```bash
# 检查当前 Node.js 版本
node --version

# 如果版本低于 22，需要升级
# 方法一：使用 nvm（推荐）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22

# 方法二：直接安装
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs
```

**步骤 2**：验证安装

```bash
node --version  # 应显示 v22.x.x
npm --version   # 应显示 10.x.x 或更高
```

**步骤 3**：安装 Git（如未安装）

```bash
sudo apt update
sudo apt install -y git
```

### 2.2 一键脚本安装（强烈推荐）

**国内镜像一键安装**（推荐）：

```bash
curl -fsSL https://open-claw.org.cn/install-cn.sh | bash
```

**官方原版安装**：

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**安装过程说明**：
- 自动检测系统环境
- 安装 Node.js（如未安装）
- 下载 OpenClaw 源码
- 安装依赖包
- 配置环境变量
- 创建必要的目录

### 2.3 其他安装方式

#### 方式 2：npm/pnpm 手动安装

```bash
# 配置国内镜像
npm config set registry https://registry.npmmirror.com

# 全局安装
npm install -g openclaw@latest

# 或使用 pnpm
npm install -g pnpm
pnpm add -g openclaw@latest

# 初始化配置
openclaw onboard
```

#### 方式 3：源码编译安装

```bash
# 克隆 Gitee 国内镜像
git clone https://gitee.com/OpenClaw-CN/openclaw-cn.git
cd openclaw-cn

# 安装 pnpm
npm install -g pnpm

# 安装依赖并构建
pnpm install
pnpm ui:build
pnpm build

# 全局链接
pnpm link --global

# 初始化
openclaw onboard --install-daemon
```

#### 方式 4：Docker 部署

```bash
# 拉取镜像
docker pull openclaw/openclaw:latest

# 启动容器
docker run -d \
  --name openclaw \
  -p 18789:18789 \
  -v ~/openclaw-data:/app/data \
  openclaw/openclaw:latest
```

### 2.4 验证安装

```bash
# 检查命令是否可用
which openclaw

# 查看版本号
openclaw --version

# 查看帮助
openclaw --help
```

### 2.5 常见问题处理

#### 问题 1：命令不存在

```bash
# 查看 npm 全局路径
npm config get prefix

# 添加到 PATH
echo 'export PATH=$(npm config get prefix)/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

#### 问题 2：权限不足

```bash
# 修改目录权限
sudo chown -R $USER:$USER ~/.local/bin
sudo chown -R $USER:$USER ~/.openclaw
```

#### 问题 3：端口被占用

```bash
# 查找占用端口的进程
sudo netstat -tulpn | grep 18789

# 终止进程
sudo kill -9 <PID>

# 或修改 OpenClaw 端口
openclaw config set gateway.port 18790
```

---

## 三、配置与初始化

### 3.1 运行初始化向导

```bash
openclaw onboard --install-daemon
```

### 3.2 配置向导步骤详解

向导会询问以下问题：

1. **接受风险提示**：输入 `yes`
2. **选择 Gateway 模式**：选择 `Local`（本地运行）
3. **选择认证方式**：推荐 MiniMax 或智谱 GLM
4. **输入 API Key**：粘贴你的 API Key
5. **选择默认模型**：根据提供商选择对应模型
6. **配置通信渠道**：新手可选择 `None`，后续再配置
7. **配对安全设置**：保持默认
8. **工作区目录**：保持默认
9. **安装推荐技能**：输入 `y`
10. **后台服务安装**：输入 `y`

### 3.3 保存配置信息

向导完成后会显示 Gateway Token 和 Web UI 地址，请复制保存。

### 3.4 启动并验证

```bash
# 查看服务状态
openclaw gateway status

# 启动 Gateway（如未运行）
openclaw gateway start

# 访问 Web UI
# 浏览器访问：http://127.0.0.1:18789/openclaw/
```

---

## 四、使用教程

### 4.1 基础命令

```bash
# 查看状态
openclaw gateway status
openclaw channels status

# 启动和停止
openclaw gateway start
openclaw gateway stop
openclaw gateway restart

# 查看日志
openclaw logs
openclaw logs --follow
openclaw logs --tail 100
```

### 4.2 自然语言指令示例

直接用自然语言给 OpenClaw 发命令：

**信息查询**：
```
帮我搜索 2026 年 3 月最新 AI 智能体新闻，并总结成 3 条要点
```

**文件操作**：
```
帮我在桌面新建一个文件夹，名字叫 AI 自动整理
```

**内容创作**：
```
帮我写一篇关于 OpenClaw 在 2026 年应用的短文案
```

**自动化任务**：
```
打开百度，搜索 2026 科技趋势，把前三条结果总结给我
```

**代码开发**：
```
帮我写一个 Python 函数，用于计算斐波那契数列
```

### 4.3 技能（Skills）系统

```bash
# 查看已安装技能
openclaw skills list

# 安装新技能
openclaw skills install <技能名称>

# 常用技能推荐
openclaw skills install file-organizer
openclaw skills install web-search
openclaw skills install browser-control
openclaw skills install git-helper
```

### 4.4 配置管理

```bash
# 查看所有配置
openclaw config list

# 修改配置
openclaw config set gateway.port 18790
openclaw config set models.default qwen3.5-plus

# 删除配置
openclaw config remove <配置项>
```

### 4.5 接入通讯平台

#### 接入飞书（推荐国内用户）

```bash
# 安装飞书连接器
openclaw skills install feishu-connector

# 配置（从飞书开放平台获取）
openclaw config set feishu.app_id cli_xxxxxxxxxxxx
openclaw config set feishu.app_secret xxxxxxxxxxxxxx

# 重启
openclaw gateway restart
```

#### 接入 Telegram

```bash
# 安装连接器
openclaw skills install telegram-connector

# 配置 Bot Token（从@BotFather 获取）
openclaw config set telegram.bot_token 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz

# 重启
openclaw gateway restart
```

---

## 五、国冿API 配置

### 5.1 MiniMax（推荐）

**特点**：国内直连，响应快，有免费额庿
**配置步骤**＿
1. 访问 https://platform.minimaxi.com 注册并获叿API Key
2. 配置 OpenClaw＿
```bash
openclaw config set models.provider minimax
openclaw config set models.api_key sk-xxxxxxxxxxxxxxxx
openclaw config set models.default abab6.5
```

3. 验证：`openclaw models test`

### 5.2 智谱 GLM（推荐）

**特点**：性价比高，中文支持优秀

**配置步骤**＿
1. 访问 https://open.bigmodel.cn 注册并获叿API Key
2. 配置＿
```bash
openclaw config set models.provider zhipu
openclaw config set models.api_key xxxxxxxxxxxxxxxxxx
openclaw config set models.default glm-4
```

3. 重启验证：`openclaw gateway restart`

### 5.3 阿里云百炼（通义千问＿
```bash
openclaw config set models.provider bailian
openclaw config set models.api_key sk-xxxxxxxxxxxxxxxx
openclaw config set models.default qwen-max
```

### 5.4 火山方舟（豆包）

```bash
openclaw config set models.provider volcengine
openclaw config set models.api_key xxxxxxxxxxxxxxxxxx
openclaw config set models.default doubao-pro-4k
```

### 5.5 本地模型（Ollama＿
**完全免费，隐私性好**

```bash
# 安装 Ollama
curl -fsSL https://ollama.ai/install.sh | bash

# 下载模型
ollama pull llama3
ollama pull qwen2

# 配置 OpenClaw
openclaw config set models.provider ollama
openclaw config set models.default llama3
openclaw config set ollama.host http://127.0.0.1:11434

# 验证
openclaw gateway restart
openclaw models test
```

---

## 六、常见问颿A&Q

### Q1: openclaw 命令不存在？

**解决方案**＿
```bash
# 添加刿PATH
echo 'export PATH=$(npm config get prefix)/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Q2: Windows 执行策略禁止运行脚本＿
**解决方案**（在 Windows PowerShell 管理员模式）＿
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```

### Q3: 端口 18789 被占用？

**解决方案**＿
```bash
# 查找并终止占用进稿sudo netstat -tulpn | grep 18789
sudo kill -9 <PID>

# 或修改端叿openclaw config set gateway.port 18790
openclaw gateway restart
```

### Q4: Gateway 无法启动＿
**解决方案**＿
```bash
# 查看详细日志
openclaw logs --tail 100

# 检柿API Key 配置
openclaw config list | grep api

# 手动启动调试
openclaw gateway start --verbose
```

### Q5: API Key 配置后仍然报错？

**解决方案**＿
1. 验证 API Key 格式是否正确
2. 检柿Base URL 配置
3. 测试 API 连接＿```bash
curl -X POST https://api.minimaxi.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"abab6.5","messages":[{"role":"user","content":"hello"}]}'
```
4. 确认账户余额充足

### Q6: 如何接入微信/飞书＿
**接入飞书**＿1. `openclaw skills install feishu-connector`
2. 在飞书开放平台创建应甿3. 配置 App ID 咿Secret
4. 重启 Gateway

**接入微信**：目前需要通过企业微信或第三方桥接服务〿
### Q7: 技能安装失败？

**解决方案**＿
```bash
# 使用 Git 代理
git config --global http.proxy http://127.0.0.1:7890

# 手动克隆
cd ~/.openclaw/skills
git clone https://github.com/user/skill-name.git
```

### Q8: 如何备份和恢复配置？

**备份**＿```bash
tar -czf openclaw-backup-$(date +%Y%m%d).tar.gz ~/.openclaw/
```

**恢复**＿```bash
tar -xzf openclaw-backup-20260328.tar.gz -C ~/
```

### Q9: 如何查看资源使用情况＿
```bash
# 查看内存使用
ps aux | grep openclaw

# 查看端口占用
netstat -tulpn | grep openclaw

# 查看磁盘使用
du -sh ~/.openclaw/
```

### Q10: 如何升级到最新版本？

```bash
# 使用 npm 更新
npm update -g openclaw

# 或使用安装脚本重新安裿curl -fsSL https://open-claw.org.cn/install-cn.sh | bash
```

---

## 七、卸载指卿
### 7.1 完全卸载 OpenClaw

**步骤 1**：停止所有相关服势
```bash
openclaw gateway stop
pkill -f openclaw
```

**步骤 2**：删除安装目彿
```bash
rm -rf ~/.openclaw
rm -rf ~/.cache/openclaw
rm -rf ~/.local/share/openclaw
```

**步骤 3**：卸载全局匿
```bash
npm uninstall -g openclaw
```

**步骤 4**：清理环境变釿
```bash
nano ~/.bashrc
# 删除 OpenClaw 相关衿source ~/.bashrc
```

### 7.2 删除系统服务

```bash
sudo systemctl stop openclaw
sudo systemctl disable openclaw
sudo rm /etc/systemd/system/openclaw.service
sudo systemctl daemon-reload
```

### 7.3 清理防火墙规刿
```powershell
# 圿Windows PowerShell 中执衿Remove-NetFirewallRule -DisplayName "OpenClaw-*"
```

### 7.4 清理 Docker 容器（如使用＿
```bash
docker stop openclaw
docker rm openclaw
docker rmi openclaw/openclaw:latest
```

### 7.5 完全清理检柿
```bash
# 检查残留文仿find ~ -name "*openclaw*" 2>/dev/null

# 检查进稿ps aux | grep openclaw

# 检查端叿netstat -tulpn | grep 18789
```

---

## 八、性能调优建议

### 8.1 资源配置优化

#### 内存优化

圿`~/.openclaw/config.json` 中添加：

```json
{
  "resourceLimits": {
    "maxMemory": "2GB",
    "maxCPU": "50%"
  }
}
```

#### CPU 优化

```bash
openclaw config set concurrency.maxTasks 4
openclaw config set concurrency.maxWorkers 2
```

### 8.2 网络优化

#### 配置代理加逿
```bash
openclaw config set proxy.http http://127.0.0.1:7890
openclaw config set proxy.https http://127.0.0.1:7890
openclaw config set network.timeout 30000
```

#### CDN 加逿
```bash
openclaw config set registry.npm https://registry.npmmirror.com
openclaw config set registry.pnpm https://registry.npmmirror.com
```

### 8.3 缓存优化

```bash
# 启用缓存
openclaw config set cache.enabled true
openclaw config set cache.maxSize 500MB
openclaw config set cache.ttl 3600

# 定期清理
openclaw cache clean

# 自动清理（每周日凌晨 2 点）
crontab -e
# 添加＿ 2 * * 0 openclaw cache clean
```

### 8.4 日志优化

```bash
# 调整日志级别
openclaw config set logLevel warning

# 日志轮转
openclaw config set logRotation.enabled true
openclaw config set logRotation.maxSize 10MB
openclaw config set logRotation.maxFiles 5
```

### 8.5 启动优化

```bash
# 预加载常用技胿openclaw config set preload.skills "[\"file-manager\", \"web-search\", \"browser-control\"]"

# 禁用不必要的技胿openclaw skills disable unused-skill-name
```

### 8.6 监控与告譿
```bash
# 配置健康检柿openclaw config set healthCheck.enabled true
openclaw config set healthCheck.interval 60

# 配置告警通知
openclaw config set alerts.email admin@example.com
openclaw config set alerts.threshold.memory 80
openclaw config set alerts.threshold.cpu 90
```

### 8.7 备份策略

**自动化备份脚朿*（保存为 `~/backup-openclaw.sh`）：

```bash
#!/bin/bash
BACKUP_DIR=~/backups/openclaw
DATE=$(date +%Y%m%d)
mkdir -p $BACKUP_DIR

# 备份配置
cp -r ~/.openclaw/config $BACKUP_DIR/config-$DATE
cp -r ~/.openclaw/skills $BACKUP_DIR/skills-$DATE

# 压缩备份
tar -czf $BACKUP_DIR/openclaw-backup-$DATE.tar.gz \
  $BACKUP_DIR/config-$DATE \
  $BACKUP_DIR/skills-$DATE

# 保留最迿7 天的备份
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete

echo "Backup completed: $BACKUP_DIR/openclaw-backup-$DATE.tar.gz"
```

**定时备份**（每天凌晿3 点）＿
```bash
crontab -e
# 添加＿ 3 * * * /home/heyang/backup-openclaw.sh
```

### 8.8 团队协作优化

```bash
# 导出团队配置
openclaw config export team-config.json

# 团队成员导入
openclaw config import team-config.json

# 统一技能版朿openclaw plugins list --json > plugins.json
```

### 8.9 定期维护建议

**每周执行一欿*＿
```bash
# 清理缓存
openclaw cache clean

# 清理日志
openclaw logs clean --days 7

# 检查更斿openclaw check-update
```

**每月执行一欿*＿
```bash
# 清理旧数捿openclaw db cleanup --days 30

# 重新安装技胿openclaw skills update --all
```

---

## 结语

恭喜你完房OpenClaw 的安装和配置！现在你已经拥有了一个强大的 AI 智能体助手〿
**下一步建访*＿- 尝试用自然语言绿OpenClaw 下达指令
- 探索各种技能和插件
- 接入飞书房Telegram 等通讯平台
- 参与社区分享使用经验

**官方资源**＿- OpenClaw 官网：https://openclaw.ai
- 官方文档：https://docs.openclaw.ai
- GitHub：https://github.com/OpenClaw-CN/openclaw
- 社区论坛：https://community.openclaw.ai

**实用技巿*＿- 多用 `--help` 查看命令帮助
- 定期更新获取最新功胿- 合理配置 API Key 避免超额使用
- 重要操作前做好备仿
祝你使用愉快！🦿
