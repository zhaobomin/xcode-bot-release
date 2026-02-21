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

编辑 `.env` 文件，至少配置以下必需项：

```bash
XCODE_API_TOKEN=your_api_token_here
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
# 最简单的方式：仅指定项目名称（自动生成 project-code）
./xbot-init --project-name "my-project"

# 使用默认配置
./xbot-init --config templates/xbot-init.yaml

# 自定义项目信息
./xbot-init --config templates/xbot-init.yaml --project-name my-project --project-code my-project

# 回滚项目（仅限 xbot-init 创建的项目）
./xbot-init rollback --project-code my-project
```

**配置模板**：`templates/xbot-init.yaml`

支持占位符（执行时自动替换）：
- `{project_name}` - 项目名称
- `{project_code}` - 项目代码
- `{project_slug}` - 项目标识

### xbot-prompt - 项目环境同步

同步项目的 Agent、Rules 和 Skills 到本地工作区。

```bash
# 更新模式（默认）：同步最新的项目配置
./xbot-prompt --mode update --project-code my-project

# 初始化模式：首次创建项目环境
./xbot-prompt --mode init --project-code my-project

# 推送模式：将本地修改推送到服务器
./xbot-prompt --mode push --project-code my-project

# 指定工作区根目录
./xbot-prompt --mode update --project-code my-project --output-root /path/to/workspace

# 强制覆盖已有目录（仅 init 模式）
./xbot-prompt --mode init --project-code my-project --force
```

**模式说明**：

| 模式 | 用途 | 说明 |
|------|------|------|
| `init` | 初始化 | 首次创建项目环境，如果目录已存在会报错（除非使用 `--force`） |
| `update` | 更新 | 同步最新的项目配置到本地，默认模式 |
| `push` | 推送 | 检测本地文件变化并推送到服务器，仅更新有变化的文件 |

**参数说明**：

- `--mode`: 操作模式，可选值 `init`|`update`|`push`，默认 `update`
- `--project-code`: 项目代码，不传则从机器人配置自动推断
- `--output-root`: 工作区根目录，默认自动检测 Git 仓库根目录或当前目录
- `--force`: 强制覆盖目标目录（仅 `init` 模式有效）

**项目 code 优先级**：
1. 命令行参数 `--project-code`
2. 机器人关联的项目代码（robot.project.code）
3. 机器人配置中的项目代码（robot.config.project_code）

**输出文件结构**：
```
.xbot_prompt/
├── agent/              # Agent 配置文件
├── rules/              # Rules 配置文件
├── skills/             # Skills 配置文件
├── AGENTS.md           # 项目环境总览
└── manifest.json       # 同步记录和文件校验信息
```

### xbot-requirement - 需求上传

手动上传单个或多个需求文档。

```bash
# 上传文件
./xbot-requirement --file task.md --project-code my-project

# 上传目录（递归上传 .md/.sql 文件）
./xbot-requirement --file ./docs --project-code my-project

# 指定需求类型
./xbot-requirement --file task.md --project-code my-project --type feature
```

**参数说明**：
- `--file`: 文件或目录路径
- `--project-code`: 项目代码
- `--type`: 需求类型（`feature`|`design`|`bugfix`）
- `--name`: 需求名称（可选）

### xbot-task-local - 本地任务执行

在本地工作区执行单次任务。

```bash
# 使用默认配置
./xbot-task-local

# 使用在线配置
./xbot-task-local --online-config online_config_example.json

# 使用任务规格模板
./xbot-task-local --spec templates/xbot_spec.template
```

### xbot-task-remote - 远程任务执行

远程执行服务器分配的任务。

```bash
./xbot-task-remote
```

## 配置说明

### 核心配置

```bash
# API 配置
XCODE_API_URL=https://api.alrun.cn
XCODE_API_TOKEN=your_token_here
XCODE_ROBOT_ID=your_robot_id_here

# 项目配置
XCODE_PROJECT_CODE=my-project

# 数据库（SQL worker 可选）
DATABASE_URL=postgresql://user:pass@host:5432/dbname
```

### 模型命令配置

为不同模型配置执行命令，未配置的模型任务会被自动跳过。

```bash
XCODE_MODEL_CODEX_CMD="codex exec --full-auto"
XCODE_MODEL_QWEN_CMD="qwen -y --model deepseek-v3.2"
XCODE_MODEL_GPT4_CMD="openai gpt-4 --auto"
XCODE_MODEL_CLAUDE_CMD="anthropic claude-2 --auto"
XCODE_MODEL_GEMINI_CMD="gemini -y -m flash -p"
XCODE_MODEL_CUSTOM_CMD="your-custom-command"
```

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
