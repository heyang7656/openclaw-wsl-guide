# WSL 环境下 Opencode 安装与使用教程

本教程详细介绍在 WSL（Ubuntu）环境中安装、配置和使用 Opencode 的完整流程。Opencode 是 OpenClaw 的前置依赖工具，为智能编码功能提供底层支持。

## 前置条件

在开始安装之前，请确保：

- **操作系统**：WSL 2 + Ubuntu 20.04 LTS 或更高版本（推荐 22.04 LTS）
- **系统更新**：已执行 `sudo apt update && sudo apt upgrade`
- **基础工具**：已安装 curl、wget、git
- **网络连接**：稳定的网络连接（用于下载安装脚本）
- **磁盘空间**：至少 2GB 可用空间
- **前置步骤**：已完成 WSL 安装和网络配置

**预计安装时间**：约 5-10 分钟

---

## 目录

- [一、Opencode 简介](#一 opencode-简介)
- [二、安装 Opencode](#二安装-opencode)
- [三、Opencode 使用教程](#三 opencode-使用教程)
- [四、常见问题 A&Q](#四常见问题-aq)
- [五、卸载指南](#五卸载指南)
- [六、性能调优建议](#六性能调优建议)

---

## 一、Opencode 简介

### 什么是 Opencode？

**Opencode** 是一个现代化的命令行开发工具，专为提升开发者工作效率而设计。它通过 AI 驱动的智能辅助和自动化工作流，帮助开发者快速完成代码编写、项目管理和部署任务。

### 核心能力

#### 1. 智能代码辅助
- **AI 驱动的代码生成**：根据自然语言描述自动生成代码片段
- **智能补全建议**：提供上下文相关的代码建议
- **代码重构优化**：自动识别并优化代码结构

#### 2. 项目模板管理
- **标准化项目结构**：快速创建符合最佳实践的项目框架
- **多技术栈支持**：支持 Python、JavaScript、Go、Rust 等主流语言
- **自定义模板**：可根据团队规范创建专属模板

#### 3. 自动化工作流
- **代码格式化**：一键统一代码风格
- **代码检查**：自动发现潜在问题和代码异味
- **测试运行**：集成测试框架，快速执行测试用例
- **构建部署**：简化编译、打包和部署流程

#### 4. 跨平台支持
- **完美兼容 WSL**：针对 WSL 环境优化
- **macOS/Linux 原生支持**：在所有主流 Unix 系统上运行
- **Windows 支持**：通过 WSL2 在 Windows 上使用

#### 5. 插件扩展系统
- **丰富的插件生态**：社区贡献的各种功能插件
- **自定义插件开发**：支持开发专属插件
- **热插拔机制**：无需重启即可加载新插件

### Opencode 与 OpenClaw 的关系

Opencode 作为 OpenClaw 的前置依赖工具，主要提供：
- **底层编码能力**：为 OpenClaw 的代码生成和处理提供支持
- **项目管理接口**：统一管理项目结构和配置
- **工具链集成**：整合常用开发工具，简化工作流

---

## 二、安装 Opencode

### 2.1 基础环境准备

**步骤 1**：安装基础工具

```bash
sudo apt update
sudo apt install -y curl wget git build-essential
```

**步骤 2**：验证网络连接

```bash
# 测试 GitHub 连接
curl -I https://github.com

# 测试 opencode.ai 连接
curl -I https://opencode.ai
```



### 2.2 一键安装 Opencode

**步骤 1**：执行安装脚本

```bash
curl -fsSL https://opencode.ai/install | bash
```
更多安装方法参考[opencode官方网站](https://opencode.ai)
**安装过程说明**：
- 自动检测系统环境
- 下载最新的 Opencode 二进制文件
- 配置环境变量
- 创建必要的配置文件

**步骤 2**：等待安装完成

安装过程约需 2-5 分钟，取决于网络速度。

### 2.3 验证安装

**步骤 1**：检查安装路径

```bash
which opencode
```

**预期输出**：`/home/heyang/.opencode/bin/opencode`

**步骤 2**：查看版本信息

```bash
opencode --version
```

**预期输出示例**：`opencode version 2.0.x`

**步骤 3**：查看帮助信息

```bash
opencode --help
```

### 2.4 配置环境变量（如需要）

如果 `which opencode` 没有找到命令，需要手动添加环境变量：

```bash
# 添加到.bashrc 或.zshrc
echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.bashrc

# 重新加载配置
source ~/.bashrc
```

### 2.5 开启 sudo 免密权限（可选）

> ⚠️ **安全警告**：开启 sudo 免密权限会降低系统安全性。仅建议在开发环境或测试环境中使用此配置。生产环境请谨慎评估风险，安装完成后可考虑恢复密码验证。

为了在后续操作中避免频繁输入密码：

**步骤 1**：编辑 sudoers 文件

```bash
sudo visudo
```

**步骤 2**：在文件末尾添加

将 `heyang` 替换为你的实际用户名：

```
heyang ALL=(ALL) NOPASSWD: ALL
```

**步骤 3**：保存退出

按 `Ctrl+O` → `Enter` → `Ctrl+X`

---

## 三、Opencode 使用教程

### 3.1 基础操作

#### 查看帮助信息

```bash
# 查看总体帮助
opencode --help

# 查看特定命令的帮助
opencode init --help
opencode create --help
```

#### 查看版本和配置

```bash
# 查看版本
opencode --version

# 查看当前配置
opencode config list

# 查看安装的插件
opencode plugins list
```

### 3.2 项目管理实操

#### 初始化新项目

**场景**：开始一个新的开发项目

```bash
# 交互式创建
opencode init my-project

# 指定类型创建
opencode init my-api --type python-fastapi
```

**操作步骤**：
1. 执行命令后，会进入交互式向导
2. 选择项目类型（Python、Node.js、Go 等）
3. 选择项目模板（Web API、CLI 工具、库等）
4. 确认项目名称和位置
5. 等待项目结构生成

**生成的项目结构示例**（Python FastAPI）：

```
my-api/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── routes/
│   │   └── api.py
│   └── models/
│       └── user.py
├── tests/
│   └── test_api.py
├── requirements.txt
├── pyproject.toml
└── README.md
```

#### 使用模板创建项目

**步骤 1**：查看可用模板

```bash
opencode templates list
```

**预期输出**：
```
Available templates:
  - python-fastapi    FastAPI Web 应用
  - python-flask      Flask Web 应用
  - nodejs-express    Express.js 后端
  - go-gin            Gin Web 框架
  - rust-actix        Actix-web 框架
  - cli-python        Python CLI 工具
  - library-ts        TypeScript 库
```

**步骤 2**：使用模板创建

```bash
# 创建 FastAPI 项目
opencode create python-fastapi my-web-api

# 创建 Node.js 项目
opencode create nodejs-express my-backend
```

### 3.3 代码质量管控

#### 代码格式化

**场景**：统一团队代码风格

```bash
# 格式化当前项目
opencode format

# 格式化指定目录
opencode format ./src

# 检查格式问题（不自动修复）
opencode format --check
```

**支持的格式化工具**：
- Python: Black, isort
- JavaScript/TypeScript: Prettier
- Go: gofmt
- Rust: rustfmt

#### 代码检查

**场景**：发现潜在问题和代码异味

```bash
# 运行代码检查
opencode lint

# 检查指定文件
opencode lint ./app/main.py

# 生成检查报告
opencode lint --output report.html
```

**检查内容**：
- 语法错误
- 未使用的变量和导入
- 代码复杂度
- 安全漏洞
- 性能问题

#### 运行测试

**场景**：执行自动化测试

```bash
# 运行所有测试
opencode test

# 运行指定测试文件
opencode test tests/test_api.py

# 带覆盖率报告
opencode test --coverage

# 实时监听模式
opencode test --watch
```

### 3.4 构建与部署

#### 构建项目

**场景**：编译和打包项目

```bash
# 构建项目
opencode build

# 生产环境构建
opencode build --release

# 指定输出目录
opencode build --output ./dist
```

#### 部署项目

**场景**：部署到服务器或云平台

```bash
# 部署到远程服务器
opencode deploy

# 部署到 Docker
opencode deploy --target docker

# 部署到 Kubernetes
opencode deploy --target k8s
```

**首次部署配置**：

```bash
# 配置部署目标
opencode config set deploy.target ssh
opencode config set deploy.host your-server.com
opencode config set deploy.user deploy
opencode config set deploy.path /var/www/my-app
```

### 3.5 配置管理

#### 查看和修改配置

```bash
# 查看所有配置
opencode config list

# 设置配置项
opencode config set editor.codeFormatter black
opencode config set project.pythonVersion 3.11

# 删除配置项
opencode config remove project.pythonVersion

# 导出配置
opencode config export > my-config.json

# 导入配置
opencode config import my-config.json
```

#### 常用配置项

```bash
# 设置默认编辑器
opencode config set editor.default vscode

# 设置代码格式化器
opencode config set editor.codeFormatter prettier

# 设置 Python 版本
opencode config set project.pythonVersion 3.11

# 设置 Node.js 版本
opencode config set project.nodeVersion 20

# 启用自动保存
opencode config set editor.autoSave true
```

### 3.6 插件管理

#### 查看和管理插件

```bash
# 列出已安装插件
opencode plugins list

# 查看插件详情
opencode plugins show <插件名称>

# 搜索可用插件
opencode plugins search <关键词>
```

#### 安装和更新插件

```bash
# 安装新插件
opencode plugins install <插件名称>

# 从 URL 安装
opencode plugins install https://github.com/user/plugin-repo

# 更新插件
opencode plugins update <插件名称>

# 更新所有插件
opencode plugins update --all

# 卸载插件
opencode plugins uninstall <插件名称>
```

#### 推荐插件

```bash
# Python 开发
opencode plugins install python-linter
opencode plugins install pytest-helper

# Web 开发
opencode plugins install html-formatter
opencode plugins install css-validator

# Git 集成
opencode plugins install git-integration
opencode plugins install commit-message-linter
```

### 3.7 日志查看

```bash
# 查看最近日志
opencode logs

# 查看最近 50 行
opencode logs --tail 50

# 实时跟踪日志
opencode logs --follow

# 查看错误日志
opencode logs --level error

# 导出日志
opencode logs --output opencode-log.txt
```

---

## 四、常见问题 A&Q

### Q1: opencode 命令找不到？

**问题现象**：执行 `opencode` 提示 `command not found`

**解决方案**：

1. **检查安装路径**：
   ```bash
   ls -la ~/.opencode/bin/
   ```

2. **添加到 PATH**：
   ```bash
   echo 'export PATH="$HOME/.opencode/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **验证**：
   ```bash
   which opencode
   ```

### Q2: 安装脚本执行失败？

**问题现象**：`curl` 命令报错或超时

**解决方案**：

1. **检查网络连接**：
   ```bash
   curl -I https://opencode.ai
   ```

2. **使用国内镜像**（如有）：
   ```bash
   curl -fsSL https://mirror.example.com/opencode/install | bash
   ```

3. **手动下载安装**：
   ```bash
   cd /tmp
   curl -LO https://github.com/opencode/opencode/releases/latest/download/opencode-linux-amd64.tar.gz
   tar -xzf opencode-linux-amd64.tar.gz
   sudo mv opencode /usr/local/bin/
   ```

### Q3: 权限不足无法安装？

**问题现象**：提示 `Permission denied`

**解决方案**：

1. **使用 sudo**：
   ```bash
   curl -fsSL https://opencode.ai/install | sudo bash
   ```

2. **或修改目录权限**：
   ```bash
   sudo chown -R $USER:$USER ~/.opencode
   ```

### Q4: 项目初始化失败？

**问题现象**：`opencode init` 报错

**解决方案**：

1. **检查目录是否为空**：
   ```bash
   ls -la
   ```
   确保目标目录为空或不存在

2. **检查磁盘空间**：
   ```bash
   df -h
   ```

3. **查看详细错误**：
   ```bash
   opencode init my-project --verbose
   ```

### Q5: 代码格式化不生效？

**问题现象**：`opencode format` 执行后无变化

**解决方案**：

1. **检查是否安装了格式化器**：
   ```bash
   opencode plugins list | grep format
   ```

2. **安装对应的格式化插件**：
   ```bash
   opencode plugins install python-formatter  # Python
   opencode plugins install prettier         # JavaScript
   ```

3. **检查配置文件**：
   ```bash
   opencode config list | grep formatter
   ```

### Q6: 测试运行失败？

**问题现象**：`opencode test` 报错

**解决方案**：

1. **检查测试框架是否安装**：
   ```bash
   pip list | grep pytest    # Python
   npm list | grep jest      # Node.js
   ```

2. **安装测试依赖**：
   ```bash
   opencode install-deps --dev
   ```

3. **查看测试文件结构**：
   ```bash
   find . -name "test_*.py" -o -name "*.test.js"
   ```

### Q7: 插件安装失败？

**问题现象**：`opencode plugins install` 报错

**解决方案**：

1. **检查网络连接**：
   ```bash
   ping github.com
   ```

2. **使用 Git 代理**：
   ```bash
   git config --global http.proxy http://127.0.0.1:7890
   git config --global https.proxy https://127.0.0.1:7890
   ```

3. **手动克隆插件**：
   ```bash
   cd ~/.opencode/plugins
   git clone https://github.com/user/plugin-name.git
   ```

### Q8: 如何重置 Opencode 配置？

**解决方案**：

```bash
# 备份当前配置
cp -r ~/.opencode ~/.opencode.backup

# 删除配置目录
rm -rf ~/.opencode/config

# 重新初始化
opencode init
```

---

## 五、卸载指南

### 5.1 完全卸载 Opencode

**步骤 1**：停止相关服务

```bash
# 如果有运行的服务，先停止
pkill -f opencode
```

**步骤 2**：删除安装目录

```bash
rm -rf ~/.opencode
```

**步骤 3**：清理环境变量

```bash
# 编辑.bashrc 或.zshrc
nano ~/.bashrc

# 删除以下行
export PATH="$HOME/.opencode/bin:$PATH"
```

**步骤 4**：重新加载配置

```bash
source ~/.bashrc
```

### 5.2 清理项目配置

**删除 Opencode 创建的项目**：

```bash
# 列出生成的项目
ls ~/projects/

# 删除指定项目
rm -rf ~/projects/my-project
```

**清理全局配置**：

```bash
rm -rf ~/.config/opencode
```

### 5.3 恢复 sudoers 配置（如开启了免密）

**步骤 1**：编辑 sudoers 文件

```bash
sudo visudo
```

**步骤 2**：删除免密配置行

找到并删除类似以下的行：
```
heyang ALL=(ALL) NOPASSWD: ALL
```

**步骤 3**：保存退出

按 `Ctrl+O` → `Enter` → `Ctrl+X`

### 5.4 清理缓存文件

```bash
# 清理 Opencode 缓存
rm -rf ~/.cache/opencode

# 清理临时文件
rm -rf /tmp/opencode-*
```

---

## 六、性能调优建议

### 6.1 优化启动速度

**禁用不必要的插件**：

```bash
# 查看已启用的插件
opencode plugins list --enabled

# 禁用不常用的插件
opencode plugins disable <插件名称>
```

**预加载常用命令**：

在 `~/.bashrc` 中添加：
```bash
alias oc='opencode'
alias ocinit='opencode init'
alias occreate='opencode create'
alias ocformat='opencode format'
```

### 6.2 磁盘空间优化

**定期清理缓存**：

```bash
# 创建清理脚本
cat > ~/cleanup-opencode.sh << 'EOF'
#!/bin/bash
rm -rf ~/.cache/opencode/*
rm -rf /tmp/opencode-*
echo "Cache cleaned!"
EOF

chmod +x ~/cleanup-opencode.sh
```

**设置定时清理**（每周执行）：

```bash
crontab -e
# 添加：
0 3 * * 0 /home/heyang/cleanup-opencode.sh
```

### 6.3 网络加速配置

**配置 Git 镜像**：

```bash
git config --global url."https://ghproxy.com/".insteadOf "https://github.com/"
```

**配置 npm 镜像**（Node.js 项目）：

```bash
npm config set registry https://registry.npmmirror.com
```

**配置 pip 镜像**（Python 项目）：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 6.4 内存使用优化

**限制并发任务数**：

```bash
opencode config set build.parallelJobs 2
opencode config set test.parallelJobs 4
```

**监控资源使用**：

```bash
# 实时监控
htop

# 查看 Opencode 进程
ps aux | grep opencode
```

### 6.5 备份策略

**自动化配置备份**：

```bash
#!/bin/bash
# save as ~/backup-opencode-config.sh
BACKUP_DIR=~/backups/opencode
DATE=$(date +%Y%m%d)
mkdir -p $BACKUP_DIR
cp -r ~/.opencode/config $BACKUP_DIR/config-$DATE
tar -czf $BACKUP_DIR/opencode-config-$DATE.tar.gz $BACKUP_DIR/config-$DATE
echo "Config backup completed: $BACKUP_DIR/opencode-config-$DATE.tar.gz"
```

**项目模板备份**：

```bash
# 备份自定义模板
cp -r ~/.opencode/templates ~/backups/opencode-templates-backup
```

### 6.6 团队协作优化

**共享配置文件**：

```bash
# 导出团队配置
opencode config export team-config.json

# 团队成员导入
opencode config import team-config.json
```

**统一插件版本**：

```bash
# 导出插件列表
opencode plugins list --json > plugins.json

# 团队批量安装
opencode plugins install-from-list plugins.json
```

---

## 结语

恭喜你完成 Opencode 的安装和配置！现在你已经拥有了一个强大的智能开发助手。

**下一步**：
- 尝试使用 `opencode init` 创建你的第一个项目
- 探索各种模板和插件
- 继续阅读《OpenClaw 安装与使用教程》

**官方资源**：
- Opencode 官方文档：https://opencode.ai/docs
- 插件市场：https://opencode.ai/plugins
- 社区论坛：https://community.opencode.ai

**实用技巧**：
- 多用 `--help` 查看命令帮助
- 定期更新插件获取最新功能
- 参与社区贡献自定义模板和插件
