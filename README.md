# xbot - 智能任务机器人与需求管理工具

xbot 是一个用于自动化任务执行和需求文档管理的工具集，支持多平台运行。

## 快速开始

### 1. 克隆仓库

```bash
git clone https://github.com/zhaobomin/xcode-bot-release.git
cd xcode-bot-release
```

### 2. 选择平台

根据你的操作系统选择对应的文件夹：

- `darwin-arm64/` - macOS (Apple Silicon M1/M2/M3)
- `darwin-amd64/` - macOS (Intel 芯片)
- `linux-amd64/` - Linux (x86_64)
- `windows-amd64/` - Windows (x86_64)

进入对应平台的目录：

```bash
# macOS (Apple Silicon)
cd darwin-arm64

# macOS (Intel)
cd darwin-amd64

# Linux
cd linux-amd64

# Windows PowerShell
cd windows-amd64
```

### 3. 配置环境

复制配置模板并编辑：

```bash
cp ../.env.example .env
```

编辑 `.env` 文件，配置以下必需项：

```bash
XCODE_API_TOKEN=your_token_here
XCODE_ROBOT_ID=your_robot_id_here
```

### 4. 运行工具

根据你的需求运行对应的工具（在平台目录下执行）：

```bash
# 示例：初始化项目
./xbot-init --project-name "my-project"

# 示例：启动任务守护进程
./xbot-daemon

# Windows 用户使用：
# .\xbot-init.exe --project-name "my-project"
# .\xbot-daemon.exe
```

## 工具列表

| 工具 | 用途 |
|------|------|
| `xbot-daemon` | 持续轮询并执行任务 |
| `xbot-init` | 一键初始化项目、机器人和任务 |
| `xbot-prompt` | 项目环境同步 |
| `xbot-requirement` | 手动上传需求文档 |
| `xbot-task-local` | 本地执行单次任务 |
| `xbot-task-remote` | 远程执行单次任务 |

### xbot-daemon - 任务守护进程

持续轮询服务器并执行分配的任务。

```bash
./xbot-daemon
```

**适用场景**：服务器环境，需要持续运行。

### xbot-init - 项目初始化

一键创建项目、Agent、Rules 和初始任务。

```bash
# 最简单的方式：仅指定项目名称
./xbot-init --project-name "my-project"
```

更多参数请使用 `./xbot-init --help` 查看。

### xbot-prompt - 项目环境同步

同步项目的 Agent、Rules 和 Skills 到本地工作区。

```bash
# 更新模式（默认）：从服务器同步到本地
./xbot-prompt

# 或者显式指定 update 模式
./xbot-prompt update

# 推送模式：将本地修改的 Agent 和 Rules 推送到服务器
./xbot-prompt push

# 初始化模式：首次初始化项目环境（会覆盖现有目录）
./xbot-prompt init --force
```

**模式说明**：
- `update`（默认）：从服务器同步 Agent、Rules 和 Skills 到本地，更新本地文件
- `push`：将本地修改的 Agent 和 Rules 文件推送到服务器，仅更新有变化的文件
- `init`：首次初始化项目环境，创建目录结构并下载所有资源

更多参数请使用 `./xbot-prompt --help` 查看。

### xbot-requirement - 需求上传

手动上传单个或多个需求文档。

```bash
# 上传文件
./xbot-requirement --file task.md
```

更多参数请使用 `./xbot-requirement --help` 查看。

### xbot-task-local - 本地任务执行

在本地工作区执行单次任务。

```bash
./xbot-task-local
```

更多参数请使用 `./xbot-task-local --help` 查看。

### xbot-task-remote - 远程任务执行

远程执行服务器分配的任务。

```bash
./xbot-task-remote
```

更多参数请使用 `./xbot-task-remote --help` 查看。

## 文件结构

```
xcode-bot-release/
├── darwin-arm64/           # macOS (Apple Silicon M1/M2/M3)
│   ├── xbot-daemon
│   ├── xbot-init
│   ├── xbot-prompt
│   ├── xbot-requirement
│   ├── xbot-task-local
│   └── xbot-task-remote
├── darwin-amd64/           # macOS (Intel 芯片)
│   └── (同上)
├── linux-amd64/            # Linux (x86_64)
│   └── (同上)
├── windows-amd64/          # Windows (x86_64)
│   ├── xbot-daemon.exe
│   ├── xbot-init.exe
│   └── (同上)
├── templates/              # 配置模板
│   ├── xbot-init.yaml
│   └── xbot_spec.template
├── .env.example           # 环境变量模板
├── online_config_example.json  # 在线配置示例
└── README.md              # 本文档
```

## 常见问题

**Q: 提示 `XCODE_API_TOKEN 不能为空`？**

A: 检查 `.env` 文件是否在可执行文件同目录下，并确保 `XCODE_API_TOKEN` 已正确配置。

**Q: Windows 下无法执行程序？**

A: 确保使用 `windows-amd64/` 目录下的 `.exe` 文件，或在 PowerShell 中使用 `.\xbot-daemon.exe`。

**Q: 如何查看工具帮助信息？**

A: 运行工具时加上 `--help` 参数，例如 `./xbot-init --help`。

## 许可证

请参考项目源码仓库获取许可证信息。
