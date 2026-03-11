# Nanobot 项目完整学习教程（超详细小白版）

## 目录

1. [项目概览](#1-项目概览)
2. [安装教程 (Mac)](#2-安装教程-mac)
3. [安装教程 (Windows)](#3-安装教程-windows)
4. [快速开始](#4-快速开始)
5. [项目入口与启动流程](#5-项目入口与启动流程)
6. [项目整体目录结构](#6-项目整体目录结构)
7. [核心模块详解：`nanobot/` 包](#7-核心模块详解nanobot-包)
8. [bridge/ — WhatsApp 桥接](#8-bridge--whatsapp-桥接)
9. [tests/ — 测试套件](#9-tests--测试套件)
10. [完整消息流程图解](#10-完整消息流程图解)
11. [如何 Debug](#11-如何-debug)
12. [配置文件详解](#12-配置文件详解)
13. [Docker 部署](#13-docker-部署)
14. [关键代码阅读路线](#14-关键代码阅读路线)

---

## 1. 项目概览

### 1.1 用一句话解释：它是什么？

**nanobot** 就是你的**私人 AI 管家**，你可以通过 Telegram、微信、钉钉、邮件等任何你常用的 App 给它发消息，它会帮你做各种事情：查天气、写代码、搜索网页、管理文件、设定提醒……

想象一下这个场景：

> 你在上班路上用手机发一条 Telegram 消息给它："帮我看看今天北京天气，顺便提醒我下午3点开会"
>
> 它自动：
> 1. 查询北京天气 API
> 2. 回复你天气信息
> 3. 创建一个下午3点的定时提醒任务

**这一切都运行在你自己的电脑上**，不需要订阅任何服务，只需要你有一个 AI 模型的 API Key（比如 DeepSeek 的 API，非常便宜）。

### 1.2 和 ChatGPT 有什么区别？

| 对比项 | ChatGPT | Nanobot |
|--------|---------|---------|
| 运行位置 | OpenAI 的服务器 | 你自己的电脑 |
| 数据隐私 | 数据发给 OpenAI | 完全本地，数据不外泄 |
| 使用平台 | 只能用浏览器/App | Telegram、钉钉、WhatsApp 等任何地方 |
| 记忆能力 | 每次对话独立，无长期记忆 | 有持久化记忆，记得你的偏好 |
| 执行能力 | 只能聊天 | 能执行命令、读写文件、发邮件等 |
| 费用 | $20/月 | 按 API 用量计费，通常便宜 10 倍以上 |
| 定时任务 | 无 | 可以设置"每天早上提醒我" |

### 1.3 核心特点（小白版解释）

| 特性 | 说明 | 生活类比 |
|------|------|---------|
| 超轻量 | 核心代码仅约 4,000 行 | 就像一辆轻型摩托，不是笨重的卡车 |
| 全异步 | 基于 `asyncio`，可以同时处理多件事 | 就像服务员同时服务多桌客人 |
| 多 LLM 支持 | 支持 20+ AI 模型提供商 | 就像能换不同品牌发动机的汽车 |
| 多平台接入 | 支持 10 个聊天平台 | 就像你可以用任何手机打同一个电话 |
| 工具系统 | 内置 Shell、文件、网络等工具 | 就像瑞士军刀，有各种功能 |
| 技能系统 | 可扩展的 Markdown 技能文件 | 就像给手机安装 App |
| 记忆系统 | 两层持久化记忆 | 就像有短期记忆（今天的事）和长期记忆（你的名字、偏好） |

### 1.4 实际使用场景举例

**场景一：日常助理**
```
你（在 Telegram）: 帮我把桌面上的 report.pdf 总结一下，500字以内
AI: 正在读取 report.pdf...（自动找到你桌面的文件并阅读）
    以下是摘要：...（500字摘要）
```

**场景二：代码助手**
```
你: 我的 Python 脚本报错了，帮我看看
    [粘贴错误信息]
AI: 我来看看...（自动在你电脑上执行诊断命令）
    问题在第 23 行，原因是...，修改方案是...
    要我直接帮你改吗？
```

**场景三：自动化任务**
```
你: 每天早上8点给我发送今天的新闻摘要到这个对话
AI: 好的，我已经设置了每天8:00的定时任务。
    （第二天早上8点，你自动收到新闻摘要）
```

**场景四：文件管理**
```
你: 帮我把 Downloads 文件夹里所有超过1GB的文件列出来
AI: 正在扫描...（自动执行 shell 命令）
    找到以下文件：
    - video1.mp4 (2.3 GB)
    - backup.zip (1.5 GB)
    要删除吗？
```

### 1.5 项目基本信息

```
GitHub: https://github.com/HKUDS/nanobot
PyPI:   https://pypi.org/project/nanobot-ai/
版本:   v0.1.4.post4
作者:   香港大学数据科学实验室（HKUDS）
语言:   Python >= 3.11
```

---

## 2. 安装教程 (Mac)

### 2.1 环境准备

**系统要求：**
- macOS 10.15+（Catalina 及以上）
- Python >= 3.11
- Node.js >= 18（仅 WhatsApp 功能需要）

**第一步：检查你的 Python 版本**

打开终端（Terminal，在 Spotlight 搜索"终端"），输入：

```bash
python3 --version
```

你会看到类似这样的输出：
```
Python 3.12.3
```

如果版本低于 3.11，或者提示找不到命令，继续下一步安装。

**第二步（如需）：安装 Python**

```bash
# 先安装 Homebrew（Mac 上的包管理工具，类似 App Store）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装 Python 3.12
brew install python@3.12

# 验证
python3.12 --version
# 应该看到：Python 3.12.x
```

### 2.2 方法一：从 PyPI 安装（稳定版，推荐初学者）

这是最简单的方式，就像在 App Store 下载 App：

```bash
pip3 install nanobot-ai

# 验证安装成功
nanobot --version
# 应该看到：nanobot v0.1.4.post4
```

**如果遇到 "pip3 not found"：**
```bash
python3 -m pip install nanobot-ai
```

### 2.3 方法二：使用 uv 安装（更快，推荐）

`uv` 是一个超快的 Python 包管理器，安装速度比 pip 快 10-100 倍：

```bash
# 安装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 重启终端，或者运行：
source ~/.bashrc  # 或 source ~/.zshrc

# 安装 nanobot（非常快！）
uv tool install nanobot-ai

# 验证
nanobot --version
```

### 2.4 方法三：从源码安装（开发者推荐，想看/改代码用这个）

如果你想研究代码，甚至自己修改 nanobot，用这个方法：

```bash
# 第一步：把代码下载到你的电脑
git clone https://github.com/HKUDS/nanobot.git
cd nanobot
# 现在你在 nanobot/ 目录下，里面有所有源代码

# 第二步：创建虚拟环境（隔离的 Python 环境，避免包冲突）
python3 -m venv .venv
# 这会在 nanobot/.venv/ 创建一个独立的 Python 环境

# 第三步：激活虚拟环境（每次开发时都要做这一步）
source .venv/bin/activate
# 激活后，命令行前面会出现 (.venv) 字样

# 第四步：以"可编辑模式"安装（改了代码立刻生效，无需重装）
pip install -e .

# 第五步（可选）：安装开发工具（测试框架等）
pip install -e ".[dev]"

# 验证
nanobot --version
```

**什么是虚拟环境？** 就像在你的电脑里建一个独立的小房间，这个房间里安装的 Python 包不会影响其他项目，其他项目的包也不会影响到这里。每次进入这个项目开发时，先"进入这个房间"（激活虚拟环境）。

### 2.5 更新版本

```bash
# pip 方式
pip install -U nanobot-ai

# uv 方式
uv tool upgrade nanobot-ai
```

---

## 3. 安装教程 (Windows)

### 3.1 环境准备

**系统要求：**
- Windows 10/11（你当前用的就是 Windows 11，✓）
- Python >= 3.11
- Git（推荐）

### 3.2 安装 Python（如果还没有）

1. 打开浏览器，访问 https://www.python.org/downloads/
2. 点击大黄色按钮 "Download Python 3.12.x"
3. 运行下载的安装程序
4. **关键！** 在安装界面底部，勾选 **"Add Python to PATH"**（不勾会导致命令找不到）
5. 点击 "Install Now"

**验证安装：** 打开命令提示符（按 Win+R，输入 cmd，回车）：

```cmd
python --version
# 应该看到：Python 3.12.x

pip --version
# 应该看到：pip 24.x ...
```

> **Windows 上的注意：** Python 命令是 `python`（不是 `python3`），pip 命令是 `pip`（不是 `pip3`）

### 3.3 方法一：从 PyPI 安装（最简单）

打开命令提示符（CMD）或 PowerShell：

```powershell
pip install nanobot-ai

# 验证
nanobot --version
```

**如果报错 "nanobot 不是内部或外部命令"：**
```powershell
# 方法1：用 Python 模块方式运行
python -m nanobot --version

# 方法2：找到脚本位置并添加到 PATH
# 通常在：C:\Users\你的用户名\AppData\Local\Programs\Python\Python312\Scripts\
```

### 3.4 方法二：使用 uv 安装（推荐）

打开 PowerShell（以管理员身份）：

```powershell
# 安装 uv
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# 重新打开 PowerShell，然后：
uv tool install nanobot-ai

# 验证
nanobot --version
```

### 3.5 方法三：从源码安装（你现在就用这个！）

你的电脑上已经有 `D:\nanobot` 目录，就是从源码安装的。让我解释你当前的状态：

```
D:\nanobot\          ← 这就是项目根目录（你已经有了）
├── nanobot/         ← Python 包源代码
├── .venv/           ← 虚拟环境（如果你建了的话）
├── pyproject.toml   ← 项目配置，定义依赖关系
└── ...
```

如果还没安装，在 Git Bash 或 PowerShell 中：

```bash
cd D:\nanobot

# 创建虚拟环境
python -m venv .venv

# 激活（Git Bash 方式）
source .venv/Scripts/activate

# 激活（PowerShell 方式）
.venv\Scripts\Activate.ps1

# 安装
pip install -e .

# 验证
nanobot --version
```

### 3.6 Windows 特殊注意事项

**中文乱码问题：** 如果输出中文显示乱码，在 CMD 中运行：

```cmd
chcp 65001
```

或者设置环境变量（永久生效）：
1. 右键"此电脑" → 属性 → 高级系统设置 → 环境变量
2. 新建系统变量：变量名 `PYTHONIOENCODING`，值 `utf-8`

项目代码在 `cli/commands.py` 中已经自动处理了这个问题：

```python
# nanobot 启动时自动设置 Windows 编码
if sys.platform == "win32":
    sys.stdout.reconfigure(encoding="utf-8", errors="replace")
```

---

## 4. 快速开始

### 步骤一：初始化工作区

```bash
nanobot onboard
```

这个命令会引导你完成初始设置。运行后你会看到类似这样的交互：

```
Welcome to nanobot! Let's get you set up.

? Enter your preferred AI provider (openrouter/anthropic/deepseek): openrouter
? Enter your API key: sk-or-v1-xxxx
? Choose a default model: anthropic/claude-opus-4-5

✓ Configuration saved to C:\Users\你的用户名\.nanobot\config.json
✓ Workspace created at C:\Users\你的用户名\.nanobot\workspace\
✓ Template files copied to workspace
```

**创建了哪些文件？**

```
~/.nanobot/                    ← 你的 nanobot 主目录
├── config.json                ← 配置文件（API Key、模型、频道等）
└── workspace/                 ← 你的工作区
    ├── AGENTS.md              ← 定义 AI 的身份和行为
    ├── SOUL.md                ← 定义 AI 的个性和价值观
    ├── USER.md                ← 你的个人信息（让 AI 更了解你）
    ├── TOOLS.md               ← 工具使用策略
    ├── HEARTBEAT.md           ← 定时任务配置
    └── memory/
        └── MEMORY.md          ← AI 的长期记忆（自动维护）
```

**Windows 上这些文件在哪里？**
通常在 `C:\Users\你的用户名\.nanobot\`，比如 `C:\Users\15372\.nanobot\`

### 步骤二：配置 API Key

如果 `onboard` 没有完成配置，手动编辑 `~/.nanobot/config.json`：

**使用 DeepSeek（国内推荐，便宜）：**

```json
{
  "providers": {
    "deepseek": {
      "apiKey": "sk-你的deepseek的key"
    }
  },
  "agents": {
    "defaults": {
      "model": "deepseek/deepseek-chat",
      "provider": "deepseek"
    }
  }
}
```

获取 DeepSeek API Key：访问 https://platform.deepseek.com/ → API Keys → 创建新 Key

**使用 OpenRouter（全球用户推荐，可以访问所有模型）：**

```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-你的key"
    }
  },
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter"
    }
  }
}
```

获取 OpenRouter Key：访问 https://openrouter.ai/keys

### 步骤三：开始对话！

**单次对话（问一个问题就退出）：**

```bash
nanobot agent -m "你好！帮我用Python写一个冒泡排序算法"
```

你会看到类似输出：
```
nanobot 🐈 is thinking...

当然！以下是冒泡排序的 Python 实现：

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr

# 测试
numbers = [64, 34, 25, 12, 22, 11, 90]
sorted_numbers = bubble_sort(numbers)
print(f"排序结果: {sorted_numbers}")
# 输出: 排序结果: [11, 12, 22, 25, 34, 64, 90]
```

算法说明：...
```

**交互模式（可以持续对话）：**

```bash
nanobot agent
```

然后你可以连续对话：
```
You: 你好！今天天气怎么样？
nanobot 🐈: 我没有直接获取天气的能力，但我可以用 web_search 工具帮你查！
            ↳ web_search("北京今天天气")
            根据搜索结果，北京今天...

You: 帮我在桌面创建一个 hello.txt 文件
nanobot 🐈: 好的！
            ↳ exec("echo 'Hello World' > ~/Desktop/hello.txt")
            文件已创建！你可以在桌面看到 hello.txt。

You: /new
nanobot 🐈: 好的，开始新的对话。之前的内容已经整合到记忆中。

You: exit
# 退出程序
```

**可以用的特殊命令：**

| 命令 | 说明 | 例子 |
|------|------|------|
| `/new` | 清空当前对话历史，开始新会话 | 当你想换个话题时用 |
| `/stop` | 停止当前正在执行的任务 | 当 AI 跑得太慢时用 |
| `/help` | 显示帮助信息 | |
| `exit` 或 `quit` | 退出程序 | |
| `Ctrl+C` | 强制退出 | |

### 步骤四：查看状态

```bash
nanobot status
```

输出示例：
```
nanobot v0.1.4.post4

Configuration: ~/.nanobot/config.json

Providers:
  ✓ openrouter  (sk-or-v1-...xxxx)
  ✗ anthropic   (not configured)
  ✗ deepseek    (not configured)

Agent Defaults:
  Model:    anthropic/claude-opus-4-5
  Provider: openrouter
  Workspace: ~/.nanobot/workspace

Channels: (none configured)
```

---

## 5. 项目入口与启动流程

### 5.1 "nanobot" 命令是怎么工作的？

当你在终端输入 `nanobot agent` 时，系统是怎么知道要执行什么代码的？

这是通过 `pyproject.toml` 中的配置实现的：

```toml
# pyproject.toml（项目根目录）
[project.scripts]
nanobot = "nanobot.cli.commands:app"
```

**翻译成人话：** "当用户输入 `nanobot` 命令时，去 `nanobot/cli/commands.py` 文件里，找到 `app` 这个对象，然后调用它"

就像快捷方式一样：`nanobot` 命令 → `nanobot/cli/commands.py` 的 `app` 对象

**也可以用这种方式运行（效果完全相同）：**

```bash
python -m nanobot agent
python -m nanobot agent -m "你好"
```

这通过 `nanobot/__main__.py` 实现：
```python
# nanobot/__main__.py（只有两行！）
from nanobot.cli.commands import app
app()
```

### 5.2 所有 CLI 子命令

运行 `nanobot --help` 查看所有命令：

```bash
nanobot --help
```

输出：
```
Usage: nanobot [OPTIONS] COMMAND [ARGS]...

Options:
  -v, --version  Show version and exit
  --help         Show this message and exit.

Commands:
  agent     Start an interactive agent session
  gateway   Start the message gateway
  onboard   Initialize nanobot configuration
  status    Show current configuration status
  channels  Manage channel connections
  provider  Manage LLM providers
```

| 命令 | 说明 | 适合什么场景 |
|------|------|-------------|
| `nanobot onboard` | 首次初始化配置 | 第一次使用时 |
| `nanobot agent` | 在终端和 AI 对话 | 日常在命令行使用 |
| `nanobot gateway` | 启动多平台网关 | 让 Telegram/Discord 等 App 都能用 |
| `nanobot status` | 查看配置状态 | 调试配置问题时 |
| `nanobot channels status` | 查看各平台连接状态 | 调试 Telegram/Discord 连接时 |
| `nanobot channels login` | 连接 WhatsApp | 首次接入 WhatsApp 时 |
| `nanobot --version` | 查看版本号 | |

### 5.3 `nanobot agent` 启动流程详解

当你运行 `nanobot agent` 时，背后发生了这些事情（用生活类比）：

```
你输入：nanobot agent
         │
         ▼
1. 读取配置文件（就像营业前检查今天的菜单和食材）
   cli/commands.py → _load_runtime_config()
   读取 ~/.nanobot/config.json

         ▼
2. 创建 LLM Provider（就像决定今天用哪个厨师）
   _make_provider() → 例如：创建 OpenRouter 提供商
   这个 Provider 负责把你的问题发给 AI 模型

         ▼
3. 创建消息总线（就像餐厅的传菜系统）
   MessageBus() → 消息在"频道"和"Agent核心"之间传递

         ▼
4. 创建定时任务服务（就像闹钟）
   CronService() → 负责在指定时间触发任务

         ▼
5. 创建 Agent 核心循环（就像雇用了一个厨师）
   AgentLoop() → 这是 AI 真正"思考"的地方

         ▼
6a. 如果你用了 -m 参数（单次模式）：
    agent_loop.process_direct("你的消息")
    等待回复 → 打印 → 退出

6b. 如果没有 -m 参数（交互模式）：
    asyncio.run(run_interactive())
    ├── 后台任务：agent_loop.run()（等待消息的循环）
    ├── 后台任务：_consume_outbound()（把 AI 回复显示出来）
    └── 主线程：等待你输入 → publish_inbound(你的消息)
```

**具体例子：你输入 "帮我查查今天天气"**

```
你的输入
  ↓
publish_inbound(InboundMessage(
    channel="cli",
    sender_id="local",
    chat_id="local",
    content="帮我查查今天天气"
))
  ↓
放入 MessageBus.inbound 队列
  ↓
AgentLoop 从队列取出消息
  ↓
ContextBuilder 组装 Prompt：
  System: "你是 nanobot，你可以使用以下工具：web_search, exec, read_file..."
  User: "帮我查查今天天气"
  ↓
发给 AI 模型（比如 Claude）
  ↓
AI 决定调用 web_search("北京今天天气 2026-03-11")
  ↓
WebSearchTool 实际执行搜索
  ↓
搜索结果发回给 AI
  ↓
AI 生成最终回复："北京今天晴，气温15-22°C..."
  ↓
publish_outbound(OutboundMessage(...))
  ↓
_consume_outbound 取出消息，打印到终端
```

### 5.4 `nanobot gateway` 和 `nanobot agent` 的区别

| 对比 | `nanobot agent` | `nanobot gateway` |
|------|-----------------|-------------------|
| 接入方式 | 只有命令行 | 命令行 + Telegram + Discord + ... |
| 适合场景 | 在电脑前使用 | 需要随时随地（手机）使用 |
| 进程生命周期 | 你关终端就停 | 长期后台运行 |
| 额外服务 | 无 | 心跳服务、频道监听 |

`nanobot gateway` 的启动流程：

```
nanobot gateway
       │
       ▼
创建所有组件（同 agent）
       ▼
额外创建：
  ChannelManager → 初始化 Telegram Bot、Discord Bot 等
  HeartbeatService → 每30分钟自动唤醒一次，检查有没有待做的事
       ▼
asyncio.gather(
    agent.run(),          # AI 核心一直在后台等待消息
    channels.start_all()  # 所有频道一直在监听新消息
)
# 这两个任务并行运行，永不退出（直到你按 Ctrl+C）
```

**例子：你在手机 Telegram 给 Bot 发消息**

```
你的手机（Telegram App）发了一条消息
    ↓ 互联网
Telegram 服务器
    ↓ Bot API（你的电脑每隔几秒拉取新消息）
你电脑上的 channels/telegram.py
    ↓ 检查权限（你的 Telegram ID 在 allowFrom 白名单里吗？）
MessageBus.inbound 队列
    ↓
AgentLoop 处理（和 agent 模式完全一样）
    ↓
MessageBus.outbound 队列
    ↓
channels/telegram.py → 调用 Telegram API 发送回复
    ↓ 互联网
你的手机收到 AI 的回复
```

---

## 6. 项目整体目录结构

### 6.1 用地图理解目录

把整个项目想象成一栋大楼：

```
D:/nanobot/（项目根目录）
├── nanobot/               ← 核心楼层（62个Python文件，最重要）
│   ├── agent/             ← 大脑层：AI的思考和决策
│   ├── bus/               ← 电话总机：消息传递中枢
│   ├── channels/          ← 大门和窗口：对外接口（Telegram等）
│   ├── config/            ← 档案室：配置管理
│   ├── providers/         ← 外包团队：AI模型调用
│   ├── session/           ← 会议记录室：对话历史存储
│   ├── cli/               ← 前台接待：命令行界面
│   ├── cron/              ← 日程秘书：定时任务
│   ├── heartbeat/         ← 值班警卫：定期检查
│   ├── skills/            ← 技能手册库：各种技能说明书
│   ├── templates/         ← 入职资料：工作区模板文件
│   └── utils/             ← 工具间：通用辅助函数
│
├── bridge/                ← 翻译官办公室：WhatsApp桥接（Node.js）
├── tests/                 ← 质检部门：27个测试文件
├── pyproject.toml         ← 大楼设计图：项目配置
├── Dockerfile             ← 快速搭建方案：Docker配置
└── README.md              ← 大楼简介：项目说明
```

### 6.2 最重要的文件（按重要性排列）

**新手必读的5个文件：**

| 文件 | 重要性 | 用途 |
|------|--------|------|
| `nanobot/agent/loop.py` | ⭐⭐⭐⭐⭐ | AI的大脑，最核心的文件 |
| `nanobot/bus/events.py` | ⭐⭐⭐⭐ | 消息的数据格式定义 |
| `nanobot/agent/context.py` | ⭐⭐⭐⭐ | 如何组装发给AI的Prompt |
| `nanobot/config/schema.py` | ⭐⭐⭐ | 配置文件的完整结构 |
| `nanobot/cli/commands.py` | ⭐⭐⭐ | 命令行入口和启动逻辑 |

**工作区文件（你可以自由编辑）：**

| 文件 | 用途 | 类比 |
|------|------|------|
| `~/.nanobot/workspace/AGENTS.md` | AI的角色设定 | 员工岗位职责书 |
| `~/.nanobot/workspace/SOUL.md` | AI的性格特征 | 员工个人简历 |
| `~/.nanobot/workspace/USER.md` | 你的个人信息 | VIP客户档案 |
| `~/.nanobot/workspace/TOOLS.md` | 工具使用策略 | 操作规程手册 |
| `~/.nanobot/workspace/HEARTBEAT.md` | 定期任务清单 | 每日待办清单 |
| `~/.nanobot/workspace/memory/MEMORY.md` | AI的长期记忆 | 笔记本 |

---

## 7. 核心模块详解：`nanobot/` 包

### 7.1 `agent/` — 核心 Agent 引擎

这是整个项目最重要的目录，就是 AI 的"大脑"。

---

#### `agent/loop.py` — Agent 主循环（最核心文件）

**文件位置：** `nanobot/agent/loop.py`，约 510 行，核心类 `AgentLoop`

这个文件就是整个框架的"大脑皮层"，协调所有事情。

**用餐厅类比理解它的5大职责：**

1. **等待顾客点单** — 从消息总线接收用户消息
2. **准备菜单信息** — 组装上下文（历史对话、记忆、技能说明）
3. **把订单交给厨师** — 调用 LLM（AI模型）
4. **厨师要用工具** — 执行工具调用（搜索网络、读文件等）
5. **把菜端给顾客** — 将结果发回消息总线

**关键参数（可在 config.json 中配置）：**

| 参数 | 默认值 | 说明 | 调整建议 |
|------|--------|------|---------|
| `max_iterations` | 40 | 每次对话最多调用工具的次数 | 复杂任务调高到60 |
| `temperature` | 0.1 | AI的"创造性"，越高越随机 | 写代码用0.1，写诗用0.7 |
| `max_tokens` | 4096 | 回复的最大长度 | 需要长文章时调高 |
| `memory_window` | 100 | 保留最近多少条消息历史 | 长对话可以调高 |
| `restrict_to_workspace` | False | 是否限制工具只能访问工作区目录 | 安全模式下设为True |

**AI 如何"思考"——用具体例子解释 `_run_agent_loop()`：**

```
用户问：帮我查一下北京今天天气，并写到 weather.txt 文件里

第 1 轮迭代：
  发给 AI：[用户消息] + [工具列表：web_search, exec, read_file, write_file...]
  AI 决定：我需要先搜索天气
  AI 返回：调用 web_search("北京 天气 今天")
  ↓ 执行 web_search
  结果：北京今天晴，最高22°C，最低8°C，风力3级

第 2 轮迭代：
  发给 AI：[之前所有消息] + [工具执行结果]
  AI 决定：搜到天气了，现在写文件
  AI 返回：调用 write_file(path="weather.txt", content="北京：晴 22/8°C")
  ↓ 执行 write_file
  结果：文件已创建

第 3 轮迭代：
  AI 决定：任务完成，给用户回复
  AI 返回：finish_reason="stop"
  内容："已查询北京今天天气（晴，22/8°C）并保存到 weather.txt"
  ↓ 循环结束！
```

**伪代码（帮助理解逻辑）：**

```python
async def _run_agent_loop(messages, tools):
    for iteration in range(max_iterations):  # 最多40轮

        response = await provider.chat_with_retry(messages, tools)

        if response.has_tool_calls:
            # AI 想用工具，就执行工具
            for tool_call in response.tool_calls:
                print(f"  ↳ {tool_call.name}({tool_call.arguments})")
                result = await tool_registry.execute(
                    tool_call.name, tool_call.arguments
                )
                messages.append({"role": "tool", "content": result})
            # 继续下一轮，让 AI 看到工具执行结果
        else:
            # AI 直接给了文字回复，任务完成！
            return response.content

    return "已达到最大迭代次数"  # 40轮都没完成
```

**工具结果截断机制（`_TOOL_RESULT_MAX_CHARS = 500`）：**

```
场景：AI 执行 exec("cat 大文件.txt")，文件有10000行

没有截断：
  10000行内容全部发给 AI，消耗大量 token，费钱！

有截断（默认500字符）：
  只发前500字符给 AI，AI 知道"有结果，但太长被截断了"
  如果 AI 需要完整内容，会分页读取
```

**支持的斜杠命令：**

```
你输入 /new   → 清空当前会话并归档记忆，开始新对话
你输入 /stop  → 取消当前正在执行的任务（AI跑太慢时用）
你输入 /help  → 显示帮助信息
```

---

#### `agent/context.py` — Prompt 构建器

**文件位置：** `nanobot/agent/context.py`，核心类 `ContextBuilder`

**它的工作：** 把所有零散信息拼成一个完整的"说明书"发给 AI。

想象你给新员工写入职资料包：

```
System Prompt 的拼接顺序（用 --- 分隔）：

1. 身份块
   你是 nanobot 🐈，一个个人AI助手
   当前时间：2026-03-11 14:30（星期三）（UTC+8）
   工作区：~/.nanobot/workspace
   操作系统：Windows 11 / Python 3.12.3
   ---

2. AGENTS.md 内容（角色职责）
   你是一个全能的个人助理...
   ---

3. SOUL.md 内容（价值观）
   我的价值观：诚实、高效、尊重用户隐私...
   ---

4. USER.md 内容（用户信息）
   用户名字：张三
   用户偏好：喜欢简洁的中文回复...
   ---

5. TOOLS.md 内容（工具策略）
   使用工具前先思考最少步骤...
   ---

6. MEMORY.md 内容（长期记忆）
   用户在做一个叫 myapp 的项目
   用户不喜欢 JavaScript
   ---

7. Always 技能（每次自动加载的技能）
   [技能内容...]
   ---

8. 可用技能摘要（按需加载的技能列表）
   - cron: 自然语言任务调度
   - weather: 天气查询
   - github: GitHub操作
```

**每条用户消息前附加的运行时上下文：**

```
[Runtime Context — metadata only, not instructions]
Current Time: 2026-03-11 14:30 (Wednesday) (UTC+8)
Channel: telegram
Chat ID: 123456789
```

**重要设计细节：** 这段运行时上下文在保存到 session 时会被自动剥离，**不会积累在历史中**。这样每次对话开始，AI 都能拿到最新的时间，而不是"5天前"的时间。

**图片支持（多模态对话）：**

```
你通过 Telegram 发了一张截图

系统会把图片转成 base64 嵌入到消息里：
user_content = [
    {
        "type": "image_url",
        "image_url": {
            "url": "data:image/png;base64,iVBORw0KGgo..."
        }
    },
    {
        "type": "text",
        "text": "这张截图里有什么错误？"
    }
]
```

---

#### `agent/memory.py` — 两层记忆系统

**文件位置：** `nanobot/agent/memory.py`，核心类 `MemoryStore`

**AI 有两个本子：**

| 本子 | 文件 | 内容 | 类比 |
|------|------|------|------|
| 结构化笔记本 | `MEMORY.md` | 长期重要事实 | 通讯录：重要固定信息 |
| 流水日记 | `HISTORY.md` | 时间戳活动日志 | 日记：每天发生了什么 |

**`MEMORY.md` 的样子（AI 自动维护）：**

```markdown
# User Profile
- Name: 张三
- Prefers: concise responses, Chinese language
- Timezone: Asia/Shanghai

# Projects
- myapp: Python web application, uses FastAPI
- nanobot-study: Currently studying nanobot source code

# Preferences
- IDE: VSCode
- Shell: bash
- Dislikes: verbose AI responses
```

**`HISTORY.md` 的样子（每次整合追加）：**

```markdown
[2026-03-10 09:15] 用户询问了冒泡排序的实现，AI提供了Python代码。
[2026-03-10 14:30] 用户要求在桌面创建 hello.txt 文件，执行成功。
[2026-03-11 08:00] 心跳任务：AI检查天气并通过Telegram发送了今日天气报告。
```

**记忆整合流程（对话超过100条时自动触发）：**

```
对话积累了100条消息（超过 memory_window）
         ↓
把这100条发给 AI，让它整理
         ↓
AI 调用虚拟工具 save_memory，填写：
  - history_entry: 一段简短摘要（"用户学习了nanobot项目..."）
  - memory_update: 更新后的完整 MEMORY.md 内容
         ↓
history_entry 追加到 HISTORY.md
memory_update 覆盖写入 MEMORY.md
         ↓
Session 的 last_consolidated 指针前移
（那100条消息被"压缩"，不再重复发给 AI）
```

**好处：** 对话可以无限长，重要信息永久保存，每次对话都能看到长期记忆。

**手动管理记忆：** 直接用记事本编辑 `~/.nanobot/workspace/memory/MEMORY.md`，告诉 AI 你的名字、偏好等，它下次对话就会记得。

---

#### `agent/skills.py` — 技能加载器

**文件位置：** `nanobot/agent/skills.py`，核心类 `SkillsLoader`

**技能是什么？** 一个 `SKILL.md` Markdown 文件，告诉 AI 如何执行特定任务的详细步骤。

**现实类比：** 就像公司的标准作业程序（SOP）。当 AI 需要处理"设置定时任务"时，它不是乱猜，而是读取 `cron/SKILL.md` 里的规范步骤。

**内置技能列表：**

| 技能目录 | 功能 | 触发场景举例 |
|----------|------|------------|
| `cron/` | 自然语言任务调度 | "每天早上8点提醒我" |
| `github/` | GitHub API 操作 | "给这个 Issue 回复" |
| `memory/` | 记忆管理 | "记住我叫张三" |
| `skill-creator/` | 创建新技能 | "帮我创建一个天气技能" |
| `summarize/` | 内容摘要 | "总结这篇文章" |
| `tmux/` | 终端复用器控制 | "在新窗口里运行这个命令" |
| `weather/` | 天气 API 集成 | "北京今天天气怎么样" |
| `clawhub/` | 从网上安装新技能 | "帮我搜索有没有股票技能" |

**SKILL.md 格式（以 weather 为例）：**

```markdown
---
name: weather
description: 查询实时天气和天气预报
always: false
available: true
---

# Weather Skill

当用户询问天气时，使用此技能。

## 步骤
1. 调用 web_search("城市名 天气 今天") 获取实时天气
2. 解析搜索结果中的温度、天气状况、风力信息
3. 以简洁格式回复用户

## 回复格式
城市：北京
日期：今天
天气：晴转多云
温度：最高22°C，最低8°C
```

**两种技能加载方式：**

- **`always: true`（自动加载）：** 每次对话都自动注入到 System Prompt，AI 一直知道怎么用
- **`always: false`（按需加载）：** 只在摘要里列出名称，AI 需要时才读取完整内容（节省 token）

**自定义技能（新手实验！）：**

```bash
# 第一步：创建技能目录
mkdir -p ~/.nanobot/workspace/skills/my-note

# 第二步：创建 SKILL.md（用记事本或 VSCode 编辑）
# 内容如下：
```

```markdown
---
name: my-note
description: 管理我的个人笔记，保存到 ~/notes/ 目录
always: false
available: true
---

# 我的笔记技能

## 使用方法
当用户说"记一笔"或"记录"时，使用此技能。

## 笔记保存规则
- 保存位置：~/notes/YYYY-MM-DD.md
- 格式：每条笔记一行，前面加上时间戳
- 用 write_file 或 edit_file 工具保存

## 示例对话
用户：记一笔，今天完成了nanobot的学习
AI：好的，我来记录。
    ↳ edit_file(path="~/notes/2026-03-11.md", ...)
    已记录！
```

```
# 第三步：重启 nanobot，新技能自动生效
nanobot agent
```

---

#### `agent/subagent.py` — 子 Agent 管理

**文件位置：** `nanobot/agent/subagent.py`，核心类 `SubagentManager`

**什么是子 Agent？**

主 AI 可以"克隆"自己，创建一个独立的后台 AI 处理耗时任务，主 AI 继续和你对话，不会卡住。

**例子：**

```
你：帮我分析我的 1000 个 Python 文件，找出所有 bug，可能要很久

主 AI：
  好的，这个任务会比较耗时，我创建一个后台任务处理。
  ↳ spawn("遍历 /myproject 目录下所有 .py 文件，找潜在bug，生成 bug_report.md")
  后台任务已启动！你可以继续和我聊其他的。

[几分钟后，子 Agent 自动完成...]

子 Agent（后台独立运行，完成后通知）：
  分析完成！共检查 1000 个文件，发现 23 个潜在问题。
  详细报告已保存到 bug_report.md。
  （通过消息总线把结果发回给你）
```

---

#### `agent/tools/` — 内置工具详解

**工具的本质：** AI 只能生成文字，通过工具获得"行动力"——执行命令、操作文件、联网等。

**所有工具都继承 `Tool` 基类：**

```python
class Tool(ABC):
    @property
    def name(self) -> str:          # 工具名称（AI用这个名字调用它）
        ...
    @property
    def description(self) -> str:   # 发给 AI 的说明
        ...
    def get_parameters(self) -> dict:  # 参数的 JSON Schema
        ...
    async def execute(self, **kwargs) -> str:  # 实际执行，返回结果字符串
        ...
```

---

**`tools/shell.py` — Shell 命令执行（`ExecTool`，名称 `exec`）**

最强大也最危险的工具，让 AI 在你电脑上执行任意命令。

**基本使用例子：**

```
# 查找文件
你：桌面上有哪些 PDF？
AI: ↳ exec("find ~/Desktop -name '*.pdf'")
    找到 3 个：report.pdf, invoice.pdf, plan.pdf

# 查看系统信息
你：我的磁盘还剩多少空间？
AI: ↳ exec("df -h")
    C盘：总共 500GB，已用 320GB，剩余 180GB

# 运行脚本
你：运行一下 test.py
AI: ↳ exec("python ~/test.py")
    运行结果：...

# 安装软件包
你：帮我安装 requests 库
AI: ↳ exec("pip install requests")
    Successfully installed requests-2.31.0
```

**安全黑名单（这些命令会被拦截）：**

```python
# 会被拒绝的危险命令
"rm -rf /"        # 删除系统所有文件（毁灭性！）
"rm -rf *"        # 删除当前目录所有文件
"format c:"       # 格式化C盘
":(){ :|:& };:"   # Fork炸弹（让系统崩溃）
"dd if=/dev/random"  # 向磁盘写随机数据

# 当 AI 尝试执行时：
# ToolError: "Command blocked for safety reasons"
```

**超时保护：** 命令运行超过60秒自动终止。

```
AI 执行了一个死循环：exec("while true; do echo hi; done")
→ 60秒后自动 kill
→ 返回：TimeoutError
```

**`restrict_to_workspace` 模式：**

```json
"tools": { "restrictToWorkspace": true }
```

开启后，`cd` 到工作区外的操作会被拒绝，适合需要安全隔离的场景。

---

**`tools/filesystem.py` — 文件系统工具（4个子工具）**

```python
# read_file：读取文件内容
# 例子：
# 你：帮我看看 config.py 里的内容
# AI: ↳ read_file("~/myproject/config.py")
# 返回文件的完整文本

# write_file：写入（覆盖）文件
# 警告：会覆盖已有内容！
# AI: ↳ write_file(path="~/output.txt", content="Hello World")

# edit_file：精确字符串替换（推荐！安全）
# 只改你指定的那一处，其他内容不动
# 例子：把配置文件里 DEBUG = False 改成 True
# AI: ↳ edit_file(
#         path="~/myproject/settings.py",
#         old_string='DEBUG = False',
#         new_string='DEBUG = True'
#     )

# list_dir：列出目录内容
# AI: ↳ list_dir("~/Desktop")
# 返回：所有文件和子目录的列表
```

**`edit_file` 为什么比 `write_file` 更安全？**

```
你有一个 200 行的配置文件，只想改第 47 行

❌ write_file 的风险：
   AI 要重写全部200行
   如果 AI 少写了某行，或者写错了，其他199行可能受影响

✓ edit_file 的安全：
   old_string: "server_port = 8080"  （精准定位）
   new_string: "server_port = 9090"  （只换这一行）
   其他199行完全不动
```

---

**`tools/web.py` — 网络工具（2个子工具）**

```python
# web_search：搜索网络（需要 Brave Search API Key）
# 获取Key：https://api.search.brave.com/（有免费额度）
# 配置：config.json → "tools.web.search.apiKey"
#
# 例子：
# 你：Python 3.14 有什么新功能？
# AI: ↳ web_search("Python 3.14 new features 2026")
# 返回：搜索结果摘要（标题、URL、描述）

# web_fetch：抓取网页纯文本
# AI: ↳ web_fetch("https://docs.python.org/3/")
# 返回：网页去掉HTML标签后的纯文本
# 内置安全检查：
#   只允许 http/https（防止读取本地文件）
#   最多5次重定向
#   超时保护
```

**不同场景选择哪个工具？**

```
想知道某件事 → web_search（快，给摘要）
想读某个具体网页的完整内容 → web_fetch（慢，给全文）

例子：
你：什么是 asyncio？
AI 应该：web_search("Python asyncio 简介") → 看摘要就够了

你：帮我读一下这篇论文 https://arxiv.org/xxx
AI 应该：web_fetch("https://arxiv.org/xxx") → 读全文
```

---

**`tools/cron.py` — 定时任务（`CronTool`，名称 `cron`）**

```python
# 设置定时任务
# 例子1：每天提醒
# 你：每天晚上11点提醒我睡觉
# AI: ↳ cron(
#         name="睡觉提醒",
#         schedule="0 23 * * *",  # Cron表达式
#         message="提醒用户该睡觉了！",
#         deliver=True,
#         channel="telegram",
#         to="你的用户ID"
#     )

# 自然语言 → Cron 表达式的转换：
# "每天早上8点"   → "0 8 * * *"
# "每周一早上"    → "0 8 * * 1"
# "每30分钟"      → "*/30 * * * *"
# "每月1号9点"    → "0 9 1 * *"
# "每两小时"      → "0 */2 * * *"
```

**Cron 表达式格式参考（5个数字的含义）：**

```
 分钟 小时 日期 月份 星期
  │    │    │    │    │
  0    8    *    *    *    → 每天8:00
  0    8    *    *    1    → 每周一8:00
*/30   *    *    *    *    → 每30分钟
  0    9    1    *    *    → 每月1号9:00
```

---

**`tools/message.py` — 主动发消息（名称 `message`）**

```python
# AI 主动给你发消息（不用等你先发）
# 例子：定时任务触发后，AI 发消息通知你

# AI 内部调用：
message(
    channel="telegram",
    chat_id="你的用户ID",
    content="提醒：今天下午3点有会议！"
)
```

**关键设计：** 如果一次对话中 AI 已经调用了 `message` 工具发出消息，它的"主回复"会被抑制，避免你收到两条重复内容。

---

**`tools/spawn.py` — 子 Agent 派生（名称 `spawn`）**

```python
# 创建后台子任务
# 参数：task（任务描述字符串）
spawn(task="分析 ~/data.csv 文件，找出销售额最高的10个产品，生成图表")

# 子任务在后台独立运行，不影响当前对话
# 完成后结果回传给你
```

---

**`tools/mcp.py` — MCP 协议工具（`MCPToolWrapper`）**

MCP（Model Context Protocol）是开放标准，让 AI 接入第三方工具服务器。

**配置例子（连接 GitHub MCP Server）：**

```json
"mcpServers": {
    "github": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-github"],
        "env": {
            "GITHUB_TOKEN": "ghp_你的token"
        }
    }
}
```

配置后，AI 就能使用 GitHub MCP Server 提供的所有工具（搜索仓库、创建 Issue 等）。

**懒加载机制：** MCP 服务器不是启动就连接，而是**第一次收到用户消息时**才连接，节省资源。

---

### 7.2 `bus/` — 消息总线

**文件位置：** `nanobot/bus/`

**用快递公司理解消息总线：**

```
频道（Telegram/Discord）= 寄件人/收件人
Agent（AI核心）= 处理包裹的工厂
消息总线 = 快递公司的传送带系统

寄件人 → [publish_inbound] → 传送带 → 工厂处理
工厂 → [publish_outbound] → 传送带 → 收件人
```

**解耦的好处：** 快递公司（消息总线）不需要知道工厂怎么工作，工厂也不需要知道快递怎么派送。添加新频道（比如加个邮件频道）完全不影响 AI 核心代码。

#### `bus/events.py` — 消息数据类

```python
# 进来的消息（用户 → AI）
@dataclass
class InboundMessage:
    channel: str       # 来自哪个平台（"telegram", "discord", "cli"）
    sender_id: str     # 谁发的（用户ID）
    chat_id: str       # 在哪个聊天里（群组ID或私聊ID）
    content: str       # 消息文字内容
    media: list[str] | None = None    # 附带的图片/文件路径
    metadata: dict | None = None      # 平台特定的额外信息

    @property
    def session_key(self) -> str:
        # 每个会话的唯一标识
        # 同一个聊天的消息共享同一个 session_key
        return f"{self.channel}:{self.chat_id}"

# 出去的消息（AI → 用户）
@dataclass
class OutboundMessage:
    channel: str    # 发往哪个平台
    chat_id: str    # 发给谁
    content: str    # 回复内容
    metadata: dict = field(default_factory=dict)
```

**具体例子：**

```python
# 张三在 Telegram 发了一条消息
msg = InboundMessage(
    channel="telegram",
    sender_id="987654321",   # 张三的Telegram用户ID
    chat_id="987654321",     # 私聊时 chat_id = sender_id
    content="帮我查天气"
)
# msg.session_key = "telegram:987654321"
# 张三的所有私聊消息共享这个session，AI记得之前说过什么

# AI 回复
reply = OutboundMessage(
    channel="telegram",
    chat_id="987654321",
    content="北京今天晴，22°C..."
)
```

#### `bus/queue.py` — 异步队列

```python
class MessageBus:
    def __init__(self):
        self.inbound = asyncio.Queue()   # 频道 → Agent
        self.outbound = asyncio.Queue()  # Agent → 频道
```

**为什么用队列？**

```
场景：3个人同时发消息

没有队列（乱套）：
  用户A、B、C 同时发消息
  AI 同时处理3个，可能回复混乱

有了队列（有序）：
  用户A → 队列[0]
  用户B → 队列[1]
  用户C → 队列[2]
  AI 按顺序处理，一个完成再处理下一个
```

---

### 7.3 `channels/` — 聊天平台集成

**文件位置：** `nanobot/channels/`

#### `channels/base.py` — 频道基类

所有频道必须实现的3个方法：

```python
class BaseChannel(ABC):
    async def start(self) -> None:
        # 启动监听（连接到平台）
        # Telegram：开始轮询 Bot API
        pass

    async def stop(self) -> None:
        # 优雅关闭
        pass

    async def send(self, chat_id: str, content: str) -> None:
        # 发消息给用户
        pass
```

**权限控制（必须配置！）：**

```python
def is_allowed(self, sender_id: str) -> bool:
    allowFrom = self.config.allowFrom

    if not allowFrom:          # [] → 拒绝所有人（默认！）
        return False
    if "*" in allowFrom:       # ["*"] → 允许所有人
        return True
    return sender_id in allowFrom  # 指定ID列表
```

**⚠️ 安全提示：** 默认 `allowFrom = []`，拒绝所有人！必须把你自己的用户ID加进去。

**如何找你的 Telegram ID：** 在 Telegram 搜索 `@userinfobot`，发任意消息，它会告诉你的数字 ID。

#### 各平台配置参考

**Telegram：**
```json
"telegram": {
    "enabled": true,
    "token": "1234567890:AAHxxxx",
    "allowFrom": ["987654321"]
}
```

**Discord：**
```json
"discord": {
    "enabled": true,
    "token": "YOUR_BOT_TOKEN",
    "allowFrom": ["你的Discord用户ID"]
}
```

**邮件（Email）：**
```json
"email": {
    "enabled": true,
    "imapHost": "imap.gmail.com",
    "imapPort": 993,
    "smtpHost": "smtp.gmail.com",
    "smtpPort": 587,
    "username": "you@gmail.com",
    "password": "应用专用密码",
    "allowFrom": ["trusted@example.com"]
}
```

---

### 7.4 `config/` — 配置系统

**完整 config.json 配置示例（含详细注释）：**

```json
{
  "providers": {
    "openrouter": { "apiKey": "sk-or-v1-xxxx" },
    "deepseek": { "apiKey": "sk-xxxx" },
    "custom": {
      "apiKey": "no-key",
      "apiBase": "http://localhost:11434/v1"
    }
  },
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter",
      "temperature": 0.1,
      "maxTokens": 4096,
      "maxToolIterations": 40,
      "memoryWindow": 100,
      "workspace": "~/.nanobot/workspace"
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "1234567890:AAHxxxx",
      "allowFrom": ["你的Telegram用户ID"]
    }
  },
  "tools": {
    "restrictToWorkspace": false,
    "web": {
      "search": { "apiKey": "BSA_xxxx" }
    },
    "exec": { "timeout": 60 }
  },
  "gateway": {
    "port": 18790,
    "heartbeat": {
      "enabled": true,
      "intervalS": 1800
    }
  }
}
```

**`allowFrom` 访问控制完整说明：**

| `allowFrom` 值 | 效果 | 什么时候用 |
|----------------|------|-----------|
| `[]`（默认） | 拒绝所有人 | 安全锁，防止别人用你的 Bot |
| `["*"]` | 允许所有人 | 公开 Bot（风险！） |
| `["123456"]` | 只允许这一个用户 | 个人专属 Bot（推荐） |
| `["123456", "789"]` | 允许这两个用户 | 家庭共用 |

---

### 7.5 `providers/` — LLM 提供商抽象层

**选择提供商的决策树：**

```
在中国大陆，不方便翻墙？
  → deepseek（便宜，中文好，推荐！）
  → dashscope（通义千问）
  → zhipu（智谱 GLM）
  → moonshot（Kimi）

有国际信用卡/海外账号？
  → openrouter（最强，可以访问所有顶级模型）
  → anthropic（直连 Claude）
  → openai（直连 GPT）

自己有 GPU，追求极致隐私？
  → custom + Ollama（完全本地，完全免费）
```

**本地 Ollama 配置（无需API Key）：**

```bash
# 1. 安装 Ollama
# 访问 https://ollama.ai/ 下载

# 2. 拉取模型
ollama pull llama3.2  # 约4GB
ollama pull qwen2.5   # 通义千问，中文优秀

# 3. 确认运行
ollama list
# NAME          SIZE
# llama3.2      4.0 GB
```

```json
{
  "providers": {
    "custom": {
      "apiKey": "no-key",
      "apiBase": "http://localhost:11434/v1"
    }
  },
  "agents": {
    "defaults": {
      "model": "qwen2.5",
      "provider": "custom"
    }
  }
}
```

**`LLMResponse` 数据结构（AI 返回的内容）：**

```python
# 情况1：AI 直接回答
LLMResponse(
    content="北京今天晴，22°C",
    has_tool_calls=False,
    finish_reason="stop"
)

# 情况2：AI 要用工具
LLMResponse(
    content=None,
    has_tool_calls=True,
    tool_calls=[
        ToolCall(name="web_search", arguments={"query": "北京天气"})
    ],
    finish_reason="tool_calls"
)

# 情况3：DeepSeek-R1 推理模型（有思考过程）
LLMResponse(
    content="最终答案：...",
    has_tool_calls=False,
    reasoning_content="让我先思考一下...首先...然后..."  # 可见推理链
)
```

---

### 7.6 `session/` — 会话管理

**存储位置：**

```
~/.nanobot/workspace/sessions/
├── telegram_987654321/     # 张三的 Telegram 私聊
│   └── session.jsonl
├── discord_111222333/      # Discord 频道
│   └── session.jsonl
└── cli_local/              # 命令行对话
    └── session.jsonl
```

**session.jsonl 的内容（每行一条消息）：**

```jsonl
{"role":"user","content":"你好","timestamp":"2026-03-11T08:00:00"}
{"role":"assistant","content":"你好！有什么我能帮你的？","timestamp":"2026-03-11T08:00:01"}
{"role":"user","content":"帮我查天气","timestamp":"2026-03-11T08:01:00"}
{"role":"assistant","content":null,"tool_calls":[{"name":"web_search","arguments":{"query":"北京天气"}}],"timestamp":"2026-03-11T08:01:01"}
{"role":"tool","content":"北京今天晴...","timestamp":"2026-03-11T08:01:03"}
{"role":"assistant","content":"北京今天晴，22°C，微风。","timestamp":"2026-03-11T08:01:04"}
```

**为什么用 JSONL（JSON Lines）格式？**

```
普通 JSON：每次保存都要重写整个文件（低效）
JSONL：每条新消息追加到文件末尾（高效！就像 git 的 append-only log）

另外：追加写入对 LLM Prompt Cache 友好
（Prompt 前缀不变，模型可以缓存计算结果，节省费用）
```

---

### 7.7 `cli/` — 命令行界面

**交互模式的特性：**

```bash
nanobot agent

# 你能用的快捷键：
# ↑ ↓  浏览历史输入（不用重复打同样的话）
# Ctrl+C  取消当前输入或退出
# 直接粘贴多行代码/文字，支持换行
```

**进度显示示例：**

```
You: 帮我分析一下项目结构并写一个README

nanobot 🐈 is thinking...

  ↳ list_dir("~/myproject")
  ↳ read_file("~/myproject/main.py")
  ↳ read_file("~/myproject/requirements.txt")
  ↳ write_file("~/myproject/README.md", ...)

好的！我已经分析了你的项目并创建了 README.md：

# MyProject

## 简介
...（Markdown 格式漂亮渲染）
```

**多实例运行：**

```bash
# 同时开两个 nanobot，分别用不同配置
# 窗口1（工作）
nanobot agent --config ~/.nanobot/config-work.json

# 窗口2（家庭）
nanobot gateway --config ~/.nanobot/config-home.json --port 18791
```

---

### 7.8 `cron/` — 定时任务

**任务存储：** `~/.nanobot/cron/jobs.json`（可直接查看和编辑）

```json
[
  {
    "id": "abc123",
    "name": "每日天气",
    "schedule": {
      "type": "cron",
      "value": "0 8 * * *",
      "tz": "Asia/Shanghai"
    },
    "payload": {
      "message": "查询北京今天天气，简洁中文回复",
      "channel": "telegram",
      "to": "987654321",
      "deliver": true
    },
    "enabled": true,
    "next_run": "2026-03-12T08:00:00+08:00"
  }
]
```

**实际对话设置多任务：**

```
你：帮我设置3个提醒：
    1. 每天早上8点发今日天气
    2. 每周五下午5点提醒总结本周工作
    3. 每月1号9点提醒交房租

AI：好的，设置三个任务：
    ↳ cron(name="每日天气", schedule="0 8 * * *", ...)
    ↳ cron(name="周五总结", schedule="0 17 * * 5", ...)
    ↳ cron(name="月初房租", schedule="0 9 1 * *", ...)
    全部设置完成！
```

---

### 7.9 `heartbeat/` — 心跳服务

**两阶段决策（节省 API 调用）：**

```
每30分钟自动触发：

阶段一（便宜的"是否"判断）：
  读取 HEARTBEAT.md 内容
  发给 AI："现在有需要处理的任务吗？回答是/否。"
  AI：否 → 不做任何事（节省token！）
  AI：是 → 进入阶段二

阶段二（完整执行，只在需要时）：
  调用完整 AgentLoop 执行具体任务
  结果发到 Telegram/Discord 等
```

**HEARTBEAT.md 配置示例：**

```markdown
# 心跳任务

## 定时检查
- 早上9点（Asia/Shanghai）：总结今日科技新闻
- 如果今天是周五：提醒总结本周工作
- 如果距上次对话超过8小时：发送问候消息
```

---

### 7.10 `skills/` — 内置技能包

**创建自定义技能的完整步骤：**

```bash
# 第一步：创建目录
mkdir -p ~/.nanobot/workspace/skills/stock-check

# 第二步：创建 SKILL.md
# 用 VSCode 或记事本创建并保存以下内容
```

```markdown
---
name: stock-check
description: 查询股票价格和涨跌情况
always: false
available: true
---

# 股票查询技能

## 触发场景
用户询问股票价格、涨跌幅、市值等信息时使用。

## 使用步骤
1. 识别用户提到的股票代码或名称
2. 使用 web_search 查询最新价格
3. 解析返回结果，提取关键数据

## 回复格式
股票：贵州茅台（600519.SH）
当前价格：1800.00 元
涨跌：+2.5%（今日）
```

```
# 第三步：重启 nanobot，新技能自动被发现
nanobot agent

# 测试技能
You: 帮我查一下贵州茅台的股价
AI（会自动读取 stock-check/SKILL.md）：
  ↳ web_search("贵州茅台 600519 今日股价")
  贵州茅台（600519）：1800元，涨幅+2.5%
```

---

### 7.11 `templates/` — 工作区模板

**强烈建议定制的文件——`USER.md`（告诉 AI 你是谁）：**

```markdown
# 关于我

## 基本信息
- 姓名：张三
- 职业：Python 后端工程师
- 所在地：北京
- 时区：Asia/Shanghai (UTC+8)

## 我的项目
- myapp：FastAPI + PostgreSQL 项目，在 ~/projects/myapp
- 正在学习 nanobot 源代码

## 我的偏好
- 语言：中文（技术术语可以英文）
- 回复风格：简洁直接，不要废话
- 编辑器：VSCode
- Python 版本：3.12

## 不喜欢的事
- 冗长的解释
- 不必要的警告
- 重复确认已经说过的事
```

**定制 `AGENTS.md`（调整 AI 行为）：**

```markdown
# 我的个人 AI 助手

## 工作原则
- 用中文回复，除非我用英文提问
- 执行删除操作前必须确认
- 遇到不确定的事情，直接说不知道，不要编造

## 擅长的事
- Python 编程（特别是 FastAPI、SQLAlchemy）
- Linux 命令行操作
- Git 工作流

## 工作区结构
- ~/projects/  所有项目
- ~/notes/     我的笔记
- ~/downloads/ 下载文件（可以随时清理）
```

---

### 7.12 `utils/` — 工具函数

```python
# nanobot/utils/helpers.py 中的常用函数

# 检测图片格式（通过"魔数"字节）
detect_image_mime(b"\xff\xd8\xff")   # → "image/jpeg"
detect_image_mime(b"\x89PNG\r\n")    # → "image/png"

# 创建目录（类似 mkdir -p）
ensure_dir("~/.nanobot/workspace/sessions/new_dir")
# 如果目录不存在，自动创建所有父目录

# 清理文件名（去掉不合法的字符）
safe_filename("report: 2026/03/11.txt")  # → "report 2026_03_11.txt"

# 分割长消息（Discord限制2000字符）
parts = split_message("很长的内容...", max_len=2000)
# ["前2000字...", "继续..."]
# AI 的长回复会自动分多条消息发送

# 同步工作区模板（升级 nanobot 后同步新的模板文件）
sync_workspace_templates("~/.nanobot/workspace")
# 只复制新文件，不覆盖你已经改过的文件！
```

---

## 8. `bridge/` — WhatsApp 桥接

**文件位置：** `bridge/`，语言：TypeScript / Node.js

### 8.1 为什么需要桥接？

WhatsApp **没有官方 Bot API**（不像 Telegram）。nanobot 通过 [Baileys](https://github.com/WhiskeySockets/Baileys) 库模拟 WhatsApp Web 浏览器客户端来实现通信。

这就像：你用手机网页版 WhatsApp 在浏览器里登录，Baileys 做了同样的事，只是在代码里自动化了这个过程。

### 8.2 架构图

```
你的手机（WhatsApp App）
         ↕ WhatsApp 协议（end-to-end 加密）
WhatsApp 服务器
         ↕ Baileys 库模拟 WhatsApp Web
Node.js 桥接程序（bridge/src/）
         ↕ WebSocket（ws://localhost:8765）
Python nanobot（channels/whatsapp.py）
         ↕ 消息总线
Agent 核心
```

### 8.3 文件结构说明

| 文件 | 说明 |
|------|------|
| `bridge/package.json` | Node.js 项目配置，版本 `nanobot-whatsapp-bridge v0.1.0` |
| `bridge/src/index.ts` | 入口点，启动 WebSocket 服务器和 WhatsApp 客户端 |
| `bridge/src/server.ts` | WebSocket 服务器，处理与 Python 的双向通信 |
| `bridge/src/whatsapp.ts` | Baileys 客户端：连接 WhatsApp、收发消息 |
| `bridge/src/types.d.ts` | TypeScript 类型定义 |

**关键依赖：**

| 依赖包 | 用途 |
|--------|------|
| `@whiskeysockets/baileys` | WhatsApp Web 协议实现（核心） |
| `ws` | WebSocket 服务器（Python↔Node.js通信） |
| `qrcode-terminal` | 在终端显示二维码（首次登录扫码用） |
| `pino` | 日志记录 |

### 8.4 使用流程（完整步骤）

**第一步：安装 Node.js（如果没有）**

Windows：访问 https://nodejs.org/ 下载 LTS 版本

验证：
```bash
node --version  # 应该 >= 18
npm --version
```

**第二步：首次登录（扫描二维码）**

```bash
nanobot channels login
```

终端会显示一个二维码，像这样：
```
█▀▀▀▀▀▀▀█ ▀▀█▀ █ █ ▀▀▀█ █▀▀▀▀▀▀▀█
█ █▀▀▀█ █ ▄▄█▄▀ ▀▄▀█▄▄▀ █ █▀▀▀█ █
█ █   █ █ ▄▀▄▄▀ ▄▄▄▀ ▄▄ █ █   █ █
...（二维码内容）

Please scan this QR code with WhatsApp on your phone:
WhatsApp → Menu → Linked Devices → Link a Device
```

用手机 WhatsApp：设置 → 已关联设备 → 关联设备 → 扫描二维码

扫描后：
```
✓ WhatsApp authenticated successfully!
Credentials saved to ~/.nanobot/whatsapp-auth/
```

**第三步：启动网关（自动启动桥接）**

```bash
nanobot gateway
```

启动后你会看到：
```
Starting WhatsApp bridge...
WhatsApp bridge connected (ws://localhost:8765)
WhatsApp channel ready
All channels started. Gateway is running.
```

**第四步：发消息测试**

在手机 WhatsApp 给你的账号发消息（给自己发，或者让其他人发给你的 Bot 绑定的号码），AI 会自动回复！

### 8.5 配置说明

```json
"channels": {
    "whatsapp": {
        "enabled": true,
        "bridgeUrl": "ws://localhost:8765",    // 桥接服务的 WebSocket 地址
        "bridgeToken": "",                      // 安全令牌（可选）
        "allowFrom": ["*"]                      // 允许哪些号码发消息
    }
}
```

### 8.6 常见问题

**问：每次重启都要重新扫码吗？**
不需要！认证信息保存在 `~/.nanobot/whatsapp-auth/`，重启后自动恢复连接。

**问：掉线了怎么办？**
```bash
# 查看状态
nanobot channels status

# 重新登录
nanobot channels login
```

**问：WhatsApp 封号风险？**
Baileys 是非官方协议，有一定封号风险（尤其是新号码频繁操作时）。建议用备用号码，不要用主号。

---

## 9. `tests/` — 测试套件

**文件位置：** `tests/`，框架：pytest + pytest-asyncio，共 27 个测试文件

### 9.1 测试是什么？为什么要有测试？

**类比：** 测试就像工厂的质检环节。代码写完之后，用自动化脚本验证"这段代码按预期工作吗？"，防止改了 A 功能不小心弄坏了 B 功能。

**好处：**
- 修改代码后自动检验是否破坏了原有功能
- 新功能有测试 = 有文档 + 有质量保证
- 调试时可以直接运行单个测试定位问题

### 9.2 运行测试

```bash
# 首先确保安装了开发依赖
pip install -e ".[dev]"

# 运行所有测试（在项目根目录）
cd D:\nanobot
pytest

# 输出示例：
# tests/test_commands.py::test_onboard_creates_config PASSED
# tests/test_loop_save_turn.py::test_save_turn_truncates_tool_result PASSED
# ...
# ====== 45 passed in 3.24s ======

# 只运行某一个文件的测试（加 -v 显示详细，-s 显示打印输出）
pytest tests/test_loop_save_turn.py -v -s

# 只运行名字里含"memory"的测试
pytest tests/ -k "memory" -v

# 测试失败时进入调试器
pytest tests/ --pdb

# 生成测试覆盖率报告（看哪些代码没被测试到）
pip install pytest-cov
pytest --cov=nanobot --cov-report=html
# 报告生成在 htmlcov/index.html，用浏览器打开
```

### 9.3 关键测试文件详解

**`tests/test_loop_save_turn.py` — Agent 循环的核心逻辑测试**

这个测试验证 AgentLoop 在保存对话轮次时的行为：

```python
# 测试：工具结果超过500字符时会被截断
def test_save_turn_truncates_tool_result():
    # 准备一个很长的工具执行结果（1000个字符）
    long_result = "a" * 1000

    # 保存这个对话轮次
    loop._save_turn(tool_result=long_result)

    # 验证：存储的结果应该被截断到500字符
    saved = session.messages[-1]["content"]
    assert len(saved) <= 500
    assert "...[truncated]" in saved  # 有截断标记
```

**`tests/test_memory_consolidation_types.py` — 记忆整合测试**

```python
# 测试：当消息数超过阈值时，记忆自动整合
async def test_memory_consolidation_triggers():
    # 创建100条消息（超过 memory_window=100 的阈值）
    for i in range(101):
        session.messages.append({"role": "user", "content": f"消息{i}"})

    # 处理新消息，应该触发记忆整合
    await agent.process("你好")

    # 验证：MEMORY.md 被更新，HISTORY.md 有新记录
    assert memory_file.exists()
    assert history_file.stat().st_size > 0
```

**`tests/test_cron_service.py` — 定时任务测试**

```python
# 测试：添加定时任务
async def test_add_cron_job():
    job = CronJob(
        name="测试任务",
        schedule=CronSchedule(type="cron", value="0 8 * * *", tz="UTC"),
        payload=CronPayload(message="hello", channel="cli", to="local")
    )

    await cron_service.add_job(job)

    # 验证：任务已保存到文件
    jobs = await cron_service.list_jobs()
    assert len(jobs) == 1
    assert jobs[0].name == "测试任务"

# 测试：到时间会触发执行
async def test_cron_job_executes_on_time():
    # 创建一个"立刻执行"的一次性任务
    job = CronJob(
        schedule=CronSchedule(type="at", value="2026-03-11 00:00:00", tz="UTC"),
        ...
    )
    # 手动触发时间检查
    await cron_service._check_and_run()
    # 验证：on_job 回调被调用了
    assert on_job_called == True
```

**`tests/test_provider_retry.py` — Provider 重试测试**

```python
# 测试：API 超时后自动重试
async def test_retry_on_timeout():
    # 模拟前两次超时，第三次成功
    call_count = 0
    async def mock_chat(*args, **kwargs):
        nonlocal call_count
        call_count += 1
        if call_count < 3:
            raise asyncio.TimeoutError()
        return LLMResponse(content="成功!", ...)

    provider.chat = mock_chat
    result = await provider.chat_with_retry(...)

    assert call_count == 3  # 重试了3次
    assert result.content == "成功!"
```

**`tests/test_tool_validation.py` — 工具参数验证测试**

```python
# 测试：传入错误类型的参数时，工具能正确处理
async def test_exec_tool_with_invalid_command():
    tool = ExecTool()
    # 传入危险命令
    result = await tool.execute(command="rm -rf /")
    assert "blocked" in result.lower()  # 应该被拦截

async def test_read_file_nonexistent():
    tool = ReadFileTool()
    result = await tool.execute(path="/不存在的文件.txt")
    assert "not found" in result.lower()  # 应该返回错误，不是崩溃
```

### 9.4 如何读懂测试（小白指南）

```python
# 一个典型的测试文件结构：

import pytest                          # 测试框架
from nanobot.agent.loop import AgentLoop  # 导入要测试的代码

class TestAgentLoop:                   # 测试类（可选）

    @pytest.fixture                    # fixture = 准备工作，每个测试前运行
    def agent(self):
        # 创建一个测试用的 AgentLoop（用假的 provider）
        return AgentLoop(
            bus=MessageBus(),
            provider=MockProvider(),   # 用假的 AI 提供商，不真正调用 API
            workspace="/tmp/test_workspace"
        )

    async def test_process_message(self, agent):  # 测试函数
        # Given（准备）：有一个消息
        msg = InboundMessage(channel="cli", content="你好")

        # When（执行）：处理这条消息
        response = await agent.process(msg)

        # Then（验证）：检查结果
        assert response is not None      # 有回复
        assert len(response) > 0        # 回复不为空
```

---

## 10. 完整消息流程图解

### 10.1 全链路图（以 Telegram 为例）

```
用户在手机 Telegram 发送："帮我查北京天气"
         │
         ↓ （Telegram 服务器→Bot API→你的电脑，约1秒）
channels/telegram.py
  TelegramChannel._handle_update()
  ├── 解析消息（提取文字、发送者ID等）
  ├── is_allowed("987654321") → True（在白名单里）
  └── bus.publish_inbound(InboundMessage(
          channel="telegram",
          sender_id="987654321",
          chat_id="987654321",
          content="帮我查北京天气"
      ))
         │
         ↓ 放入 asyncio.Queue
bus/queue.py
  MessageBus.inbound（异步队列）
         │
         ↓ AgentLoop 的 run() 循环取出消息
agent/loop.py
  AgentLoop._dispatch(msg)
  │
  ├── 1. 获取/创建 Session
  │      session/manager.py
  │      session_key = "telegram:987654321"
  │      messages = [之前的历史对话...]
  │
  ├── 2. 构建消息列表
  │      agent/context.py → ContextBuilder
  │      system_prompt = """
  │        你是 nanobot 🐈...
  │        [AGENTS.md 内容]
  │        [USER.md 内容]
  │        [MEMORY.md 内容]
  │        可用工具：web_search, exec, read_file...
  │      """
  │      messages = [
  │          {"role": "system", "content": system_prompt},
  │          ... 历史消息 ...,
  │          {"role": "user", "content": "帮我查北京天气\n[时间:14:30 来源:telegram]"}
  │      ]
  │
  ├── 3. 进入工具调用循环（最多40次）
  │      ┌─────────────────────────────┐
  │      │ 第1轮：                      │
  │      │ → 发给 Claude/DeepSeek/...  │
  │      │ ← AI决定调用 web_search     │
  │      │                             │
  │      │ tools/web.py 执行搜索：      │
  │      │ web_search("北京天气今天")   │
  │      │ 返回："晴，22°C，风力3级"   │
  │      │                             │
  │      │ 第2轮：                      │
  │      │ → 发给 AI（含搜索结果）      │
  │      │ ← AI生成最终回复，stop      │
  │      └─────────────────────────────┘
  │
  ├── 4. 保存会话
  │      session.messages 追加本轮对话
  │      写入 session.jsonl
  │      （如果消息数>100，后台触发记忆整合）
  │
  └── 5. 发布响应
         bus.publish_outbound(OutboundMessage(
             channel="telegram",
             chat_id="987654321",
             content="北京今天天气晴，最高22°C..."
         ))
         │
         ↓ ChannelManager 从 outbound 队列取出
channels/telegram.py
  TelegramChannel.send("987654321", "北京今天天气晴...")
         │
         ↓ 调用 Telegram Bot API
Telegram 服务器
         │
         ↓
你的手机收到 AI 的回复！（约2-5秒总延迟）
```

### 10.2 带多工具调用的例子

```
你：帮我把桌面的 notes.txt 内容翻译成英文，然后保存为 notes_en.txt

第1轮：
  AI 收到任务，决定先读文件
  → exec("cat ~/Desktop/notes.txt")（或 read_file）
  ← 返回："今天要做的事：1.买菜 2.写报告 3.健身"

第2轮：
  AI 看到文件内容，决定翻译后写入
  → write_file(
      path="~/Desktop/notes_en.txt",
      content="Today's tasks:\n1. Grocery shopping\n2. Write report\n3. Exercise"
    )
  ← 返回："File written successfully"

第3轮：
  AI 确认任务完成，直接回复
  ← finish_reason="stop"
  内容："已完成！notes.txt 已翻译成英文并保存为 notes_en.txt。"

（共3轮 LLM 调用 + 2次工具执行）
```

### 10.3 定时任务触发的流程

```
时间到达（例如每天8:00）
         ↓
cron/service.py
  CronService._tick() 发现有任务需要执行
         ↓
on_job(job) 回调
  → agent.process_direct(job.payload.message)
    消息内容："查询北京今天天气，用中文回复"
         ↓
AgentLoop 处理（与普通消息完全相同的流程）
         ↓
tools/message.py
  message(channel="telegram", to="987654321", content="天气报告：...")
         ↓
bus.publish_outbound(...)
         ↓
TelegramChannel.send(...)
         ↓
你的手机早上8:00收到天气推送！
```

---

## 11. 如何 Debug

### 11.1 方法一：启用详细日志（最简单，首选）

```bash
# agent 模式
nanobot agent --logs

# gateway 模式
nanobot gateway --verbose
```

你会看到类似这样的详细输出：

```
2026-03-11 14:30:01 | INFO | Received message from cli:local: "帮我查天气"
2026-03-11 14:30:01 | INFO | Session telegram:987654321 loaded (5 messages)
2026-03-11 14:30:01 | INFO | Calling LLM: openrouter/anthropic/claude-opus-4-5
2026-03-11 14:30:02 | INFO | Tool call: web_search({"query": "北京天气"})
2026-03-11 14:30:03 | INFO | Tool result (87 chars): 北京今天晴，最高22°C...
2026-03-11 14:30:03 | INFO | LLM responded with 52 tokens
2026-03-11 14:30:03 | INFO | Response sent to telegram:987654321
```

日志级别说明：
- `INFO`：正常运行信息
- `WARNING`：需要注意但不致命（如达到最大迭代次数）
- `ERROR`：错误（如 API 调用失败）

### 11.2 方法二：VSCode 调试器

**创建调试配置文件：**

在项目根目录创建 `.vscode/launch.json`（VSCode 会自动识别）：

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "nanobot agent (debug)",
            "type": "debugpy",
            "request": "launch",
            "module": "nanobot",
            "args": ["agent", "--logs"],
            "console": "integratedTerminal",
            "cwd": "${workspaceFolder}",
            "env": {
                "PYTHONPATH": "${workspaceFolder}"
            }
        },
        {
            "name": "nanobot gateway (debug)",
            "type": "debugpy",
            "request": "launch",
            "module": "nanobot",
            "args": ["gateway", "--verbose"],
            "console": "integratedTerminal",
            "cwd": "${workspaceFolder}"
        },
        {
            "name": "pytest (debug)",
            "type": "debugpy",
            "request": "launch",
            "module": "pytest",
            "args": ["tests/", "-v", "-s"],
            "console": "integratedTerminal",
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

**使用步骤：**

1. 用 VSCode 打开 `D:\nanobot` 文件夹
2. 确保安装了 **Python 扩展**（ms-python.python）
3. 在代码里点击行号左边，设置**断点**（红色小圆点）
4. 按 `F5` 或点击左侧"运行和调试"按钮
5. 选择上面配置好的启动方案

**推荐断点位置（帮助理解流程）：**

```python
# 断点1：agent/loop.py，看每次消息进来时
async def _process_message(self, msg: InboundMessage):
    # 👈 在这里打断点，查看 msg 的内容
    ...

# 断点2：agent/loop.py，看每次工具调用
result = await self.tool_registry.execute(tool_call.name, tool_call.arguments)
# 👈 在这里打断点，查看 result 的内容

# 断点3：agent/context.py，看 System Prompt 是什么
def build_system(self, ...):
    system = ...
    # 👈 在这里打断点，查看 system 的完整内容（AI看到什么）
```

### 11.3 方法三：直接用 Python 脚本测试

在项目根目录创建 `debug_test.py`，绕过 CLI 直接测试核心功能：

```python
# debug_test.py
import asyncio
from nanobot.config.loader import load_config
from nanobot.agent.loop import AgentLoop
from nanobot.bus.queue import MessageBus
from nanobot.cron.service import CronService
from nanobot.cli.commands import _make_provider

async def main():
    # 加载配置
    config = load_config()

    # 创建各组件
    provider = _make_provider(config)
    bus = MessageBus()
    cron = CronService()

    # 创建 AgentLoop（最小配置）
    agent = AgentLoop(
        bus=bus,
        provider=provider,
        workspace=config.workspace_path,
        model=config.agents.defaults.model,
        cron_service=cron,
    )

    # 直接发送消息并等待回复（不需要交互模式）
    print("Sending message...")
    response = await agent.process_direct("你好，介绍一下你自己")
    print(f"Response: {response}")

asyncio.run(main())
```

```bash
cd D:\nanobot
python debug_test.py
```

### 11.4 方法四：在关键位置添加打印语句

**调试工具调用（在 `agent/loop.py` 添加打印）：**

```python
# 找到这一行（大约230行附近）：
result = await self.tool_registry.execute(tool_call.name, tool_call.arguments)

# 在前后添加打印（临时调试用，完成后删掉）：
print(f"\n[DEBUG] Calling tool: {tool_call.name}")
print(f"[DEBUG] Arguments: {tool_call.arguments}")
result = await self.tool_registry.execute(tool_call.name, tool_call.arguments)
print(f"[DEBUG] Result ({len(result)} chars): {result[:200]}")
```

**调试 System Prompt（在 `agent/context.py` 添加打印）：**

```python
def build_system(self, ...):
    system = self._get_identity() + "\n---\n" + ...

    # 临时添加：打印完整 System Prompt
    print("\n" + "="*50 + "\nSYSTEM PROMPT:\n" + "="*50)
    print(system)
    print("="*50 + "\n")

    return system
```

### 11.5 常见问题排查手册

**问题：`nanobot` 命令找不到**

```bash
# 检查是否安装成功
pip show nanobot-ai

# 检查安装位置
pip show -f nanobot-ai | grep nanobot

# 如果是源码安装，确保激活了虚拟环境
source D:/nanobot/.venv/Scripts/activate  # Windows Git Bash
# 或
D:\nanobot\.venv\Scripts\activate.ps1     # PowerShell

# 最后的方法：用模块方式运行
python -m nanobot agent
```

**问题：API Key 错误（401 Unauthorized）**

```bash
# 查看当前配置
cat ~/.nanobot/config.json
# Windows PowerShell：
type C:\Users\你的用户名\.nanobot\config.json

# 检查 Key 是否正确（不能有多余空格）
# DeepSeek Key 以 sk- 开头
# OpenRouter Key 以 sk-or-v1- 开头
# Anthropic Key 以 sk-ant- 开头
```

**问题：Windows 中文乱码**

```bash
# 临时解决（每次打开CMD都要设置）
chcp 65001

# 永久解决：设置环境变量
# 右键"此电脑" → 属性 → 高级系统设置 → 环境变量
# 新建：PYTHONIOENCODING = utf-8

# 或者在 PowerShell 里运行前先设置
$env:PYTHONIOENCODING = "utf-8"
nanobot agent
```

**问题：工具调用超时**

```json
// config.json 中调整超时时间
"tools": {
    "exec": {
        "timeout": 120  // 默认60秒，改成120秒
    }
}
```

**问题：记忆整合失败（内存溢出或API错误）**

```bash
# 查看记忆文件是否格式正确
cat ~/.nanobot/workspace/memory/MEMORY.md

# 如果文件损坏，备份后重新初始化
cp ~/.nanobot/workspace/memory/MEMORY.md ~/MEMORY_backup.md
echo "# Memory\n\n(Fresh start)" > ~/.nanobot/workspace/memory/MEMORY.md

# 或者运行 /new 命令开始新会话（会触发整合）
```

**问题：Telegram Bot 不响应**

```bash
# 检查 Bot Token 是否正确
# 检查 allowFrom 配置（可能你的用户ID没有加进去）

# 查看你的 Telegram 用户ID
# 在 Telegram 搜索 @userinfobot，发任意消息

# 查看 gateway 日志
nanobot gateway --verbose
# 看有没有 "Message rejected: sender not in allowFrom" 这样的日志
```

**问题：AI 回复"我无法执行此操作"**

```bash
# 可能原因1：restrict_to_workspace = true，但操作在工作区外
# 解决：config.json 中把 restrictToWorkspace 改为 false

# 可能原因2：工具参数格式不对（AI生成了无效的参数）
# 解决：开启 --logs 查看工具调用详情

# 可能原因3：达到最大迭代次数（40次）
# 解决：config.json 中把 maxToolIterations 调高到 60
```

---

## 12. 配置文件详解

### 12.1 配置文件位置

```
Windows：C:\Users\你的用户名\.nanobot\config.json
Mac/Linux：~/.nanobot/config.json
```

### 12.2 完整配置示例（每个字段都有说明）

```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-xxxx"
    },
    "anthropic": {
      "apiKey": "sk-ant-xxxx"
    },
    "openai": {
      "apiKey": "sk-xxxx"
    },
    "deepseek": {
      "apiKey": "sk-xxxx"
    },
    "dashscope": {
      "apiKey": "sk-xxxx"
    },
    "custom": {
      "apiKey": "no-key",
      "apiBase": "http://localhost:11434/v1"
    }
  },

  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter",
      "temperature": 0.1,
      "maxTokens": 4096,
      "maxToolIterations": 40,
      "memoryWindow": 100,
      "reasoningEffort": null,
      "workspace": "~/.nanobot/workspace"
    }
  },

  "channels": {
    "telegram": {
      "enabled": true,
      "token": "1234567890:AAHxxxx",
      "allowFrom": ["你的Telegram用户ID"]
    },
    "discord": {
      "enabled": false,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["你的Discord用户ID"]
    },
    "whatsapp": {
      "enabled": false,
      "bridgeUrl": "ws://localhost:8765",
      "bridgeToken": ""
    },
    "feishu": {
      "enabled": false,
      "appId": "cli_xxx",
      "appSecret": "xxx"
    },
    "slack": {
      "enabled": false,
      "botToken": "xoxb-xxx",
      "appToken": "xapp-xxx"
    },
    "sendToolHints": true,
    "sendProgress": true
  },

  "tools": {
    "restrictToWorkspace": false,
    "web": {
      "search": {
        "apiKey": "BSA_xxx"
      },
      "proxy": null
    },
    "exec": {
      "timeout": 60,
      "pathAppend": ""
    },
    "mcpServers": {
      "filesystem": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "/tmp"],
        "env": {}
      }
    }
  },

  "gateway": {
    "port": 18790,
    "heartbeat": {
      "enabled": true,
      "intervalS": 1800
    }
  }
}
```

### 12.3 各配置项详解

**providers（AI 提供商配置）**

| 字段 | 说明 | 如何获取 |
|------|------|---------|
| `openrouter.apiKey` | OpenRouter 的 API Key | https://openrouter.ai/keys |
| `anthropic.apiKey` | Anthropic 官方 Key | https://console.anthropic.com/ |
| `deepseek.apiKey` | DeepSeek Key | https://platform.deepseek.com/ |
| `dashscope.apiKey` | 阿里云通义千问 Key | https://dashscope.console.aliyun.com/ |
| `custom.apiBase` | 本地 AI 地址 | Ollama 默认是 http://localhost:11434/v1 |

**agents.defaults（Agent 行为配置）**

| 字段 | 默认值 | 调整建议 |
|------|--------|---------|
| `model` | 取决于 provider | openrouter 下可填任意模型名 |
| `temperature` | 0.1 | 写代码用0.1，创意写作用0.6-0.8 |
| `maxTokens` | 4096 | 需要长文章/代码时调到8192 |
| `maxToolIterations` | 40 | 复杂任务调到60，简单问答10就够 |
| `memoryWindow` | 100 | 越大保留历史越多，但会用更多token |

**channels（聊天平台配置）**

| 字段 | 说明 |
|------|------|
| `enabled` | true=开启这个平台，false=关闭 |
| `token` | 平台给的 Bot Token |
| `allowFrom` | 白名单：允许哪些用户ID |
| `sendToolHints` | 是否在消息里显示"↳ 工具名(...)"的提示 |
| `sendProgress` | 是否实时发送"AI正在思考..."的状态 |

**tools（工具配置）**

| 字段 | 说明 |
|------|------|
| `restrictToWorkspace` | true=只能访问workspace目录 |
| `web.search.apiKey` | Brave Search API Key（网络搜索用） |
| `web.proxy` | 代理地址，如 "http://127.0.0.1:7890" |
| `exec.timeout` | Shell命令超时秒数（默认60） |
| `mcpServers` | MCP工具服务器配置 |

**gateway（网关配置）**

| 字段 | 说明 |
|------|------|
| `port` | Gateway 监听的端口号（默认18790） |
| `heartbeat.enabled` | 是否开启心跳服务 |
| `heartbeat.intervalS` | 心跳间隔秒数（1800=30分钟） |

### 12.4 常用配置场景

**场景一：国内用户，用 DeepSeek，只用命令行**

```json
{
  "providers": {
    "deepseek": { "apiKey": "sk-你的key" }
  },
  "agents": {
    "defaults": {
      "model": "deepseek/deepseek-chat",
      "provider": "deepseek"
    }
  }
}
```

**场景二：用 Telegram，接入 Claude**

```json
{
  "providers": {
    "openrouter": { "apiKey": "sk-or-v1-xxxx" }
  },
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5",
      "provider": "openrouter"
    }
  },
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "你的BotToken",
      "allowFrom": ["你的TelegramID"]
    }
  }
}
```

**场景三：完全本地，用 Ollama（免费！）**

```json
{
  "providers": {
    "custom": {
      "apiKey": "no-key",
      "apiBase": "http://localhost:11434/v1"
    }
  },
  "agents": {
    "defaults": {
      "model": "qwen2.5",
      "provider": "custom"
    }
  }
}
```

---

## 13. Docker 部署

### 13.1 为什么用 Docker？

**类比：** Docker 就像"快递打包"。把你的 nanobot 和所有依赖都打包到一个容器里，在任何有 Docker 的机器上都能一键运行，不用担心环境问题。

**适合场景：**
- 在服务器上长期运行 nanobot（不担心重启后丢失）
- 让朋友也能一键运行你的 nanobot
- 隔离环境，不污染系统

### 13.2 先安装 Docker

Windows：访问 https://www.docker.com/products/docker-desktop 安装 Docker Desktop

验证：
```bash
docker --version
# Docker version 24.x.x
```

### 13.3 构建 Docker 镜像

```bash
cd D:\nanobot

# 构建镜像（大约需要5-10分钟，会下载依赖）
docker build -t nanobot .

# 你会看到一系列构建步骤：
# Step 1/12: FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim
# Step 2/12: RUN apt-get install -y nodejs npm
# ...
# Successfully built a1b2c3d4e5f6
# Successfully tagged nanobot:latest
```

**Dockerfile 做了什么：**

```dockerfile
# 基础镜像：包含 Python 3.12 的轻量 Debian Linux
FROM ghcr.io/astral-sh/uv:python3.12-bookworm-slim

# 安装 Node.js（WhatsApp 桥接需要）
RUN apt-get install -y nodejs npm

# 把项目代码复制进容器
COPY . /app
WORKDIR /app

# 安装 Python 依赖
RUN pip install hatchling && pip install -e .

# 构建 WhatsApp 桥接
RUN cd bridge && npm install && npm run build

# 暴露 API 端口（网关用）
EXPOSE 18790

# 创建配置目录
RUN mkdir -p /root/.nanobot
```

### 13.4 使用 docker-compose

项目已经自带了 `docker-compose.yml`，推荐使用：

```bash
# 启动长期运行的网关（后台运行，-d=detach）
docker-compose up -d nanobot-gateway

# 查看运行状态
docker-compose ps

# 查看实时日志
docker-compose logs -f nanobot-gateway

# 运行一次性命令（问一个问题）
docker-compose run --rm nanobot-cli nanobot agent -m "你好"

# 停止所有容器
docker-compose down
```

### 13.5 配置文件挂载

Docker 容器运行时，你的本地 `~/.nanobot/` 目录会被挂载进容器，所以配置文件在主机上管理，改配置不需要重新构建镜像：

```yaml
# docker-compose.yml 中（已有）
volumes:
  - ~/.nanobot:/root/.nanobot  # 主机:容器
```

**也就是说：**
- 你在主机上编辑 `~/.nanobot/config.json` → 容器里立刻生效
- 容器里生成的会话、记忆文件 → 保存在主机上 `~/.nanobot/workspace/`

### 13.6 在服务器上长期运行

```bash
# 在 Linux 服务器上
git clone https://github.com/HKUDS/nanobot.git
cd nanobot

# 先在本地配置好 ~/.nanobot/config.json，上传到服务器
scp ~/.nanobot/config.json user@server:~/.nanobot/

# 在服务器上构建并启动
docker build -t nanobot .
docker-compose up -d nanobot-gateway

# 验证运行
docker-compose logs nanobot-gateway
```

---

## 14. 关键代码阅读路线

### 14.1 第一阶段：理解数据流（2小时）

**目标：** 明白消息是什么样的，怎么传递的。

```
第1步：nanobot/bus/events.py
  读这里：InboundMessage 和 OutboundMessage 的定义
  理解：什么是 channel、sender_id、chat_id、session_key

  实验：
  from nanobot.bus.events import InboundMessage
  msg = InboundMessage(channel="cli", sender_id="me", chat_id="me", content="hello")
  print(msg.session_key)  # "cli:me"

第2步：nanobot/bus/queue.py
  读这里：MessageBus 的两个 asyncio.Queue
  理解：生产者-消费者模式

第3步：nanobot/config/schema.py
  读这里：NanobotConfig 类的结构
  理解：配置文件的所有字段和默认值

第4步：nanobot/config/loader.py
  读这里：load_config() 函数
  理解：配置文件从哪里加载，如何合并默认值
```

### 14.2 第二阶段：理解 Agent 核心逻辑（3小时）

**目标：** 明白 AI 怎么处理消息、怎么调用工具。

```
第5步：nanobot/agent/tools/base.py
  读这里：Tool 抽象基类
  理解：每个工具需要实现哪些方法
  实验：看看 name, description, get_parameters, execute

第6步：nanobot/agent/tools/registry.py
  读这里：ToolRegistry 类
  理解：工具怎么注册、怎么执行

第7步：nanobot/agent/tools/shell.py（最简单的工具）
  读这里：ExecTool 类
  理解：一个完整工具的实现（含安全检查、超时）

第8步：nanobot/agent/context.py
  读这里：ContextBuilder.build_system() 方法
  理解：System Prompt 是怎么拼接出来的
  实验：在这里打断点，看 AI 实际看到什么

第9步：nanobot/agent/loop.py（最核心！）
  读这里：
  - __init__：了解所有参数
  - _run_agent_loop()：工具调用循环（核心算法！）
  - run()：后台消息监听
  - _process_message()：单条消息的完整处理流程
  理解：这是整个框架的心脏
```

### 14.3 第三阶段：理解持久化（1小时）

**目标：** 明白数据怎么保存、记忆怎么工作。

```
第10步：nanobot/session/manager.py
  读这里：SessionManager 和 Session 类
  理解：
  - 会话用 JSONL 文件存储
  - session_key 怎么唯一标识一个对话
  - get_or_create()、save() 方法

第11步：nanobot/agent/memory.py
  读这里：MemoryStore.consolidate() 方法
  理解：
  - 什么时候触发记忆整合
  - 整合的具体过程（发给LLM → 更新MEMORY.md和HISTORY.md）

第12步：nanobot/agent/skills.py
  读这里：SkillsLoader.load_all() 方法
  理解：
  - 从哪里找技能文件
  - always技能和按需技能的区别
```

### 14.4 第四阶段：理解 CLI 和启动（1小时）

**目标：** 明白 `nanobot agent` 命令怎么把所有东西组装起来。

```
第13步：nanobot/cli/commands.py
  读这里：
  - agent() 函数（处理 nanobot agent 命令）
  - gateway() 函数（处理 nanobot gateway 命令）
  - _make_provider() 函数（根据配置创建Provider）
  理解：所有组件怎么被创建和连接

第14步：nanobot/providers/registry.py
  读这里：PROVIDERS 元组
  理解：支持哪些提供商，怎么自动检测

第15步：nanobot/providers/base.py
  读这里：LLMProvider.chat_with_retry() 方法
  理解：LLM调用的重试逻辑
```

### 14.5 第五阶段：理解平台集成（按需）

**目标：** 明白怎么接入 Telegram、Discord 等平台。

```
第16步：nanobot/channels/base.py
  读这里：BaseChannel 基类
  理解：所有频道需要实现的接口

第17步：nanobot/channels/telegram.py（最完整的实现）
  读这里：TelegramChannel 类
  理解：
  - start()：怎么连接 Telegram Bot API
  - _handle_update()：怎么处理收到的消息
  - send()：怎么发消息

第18步：nanobot/channels/manager.py
  读这里：ChannelManager 类
  理解：怎么统一管理多个频道
```

### 14.6 实验建议（动手加深理解）

**实验1：观察工具调用过程**

在 `agent/loop.py` 第 ~230 行（工具执行处）添加临时打印：

```python
# 找到这一行：
result = await self.tool_registry.execute(tool_call.name, tool_call.arguments)

# 改成：
print(f"\n🔧 工具调用: {tool_call.name}")
print(f"   参数: {tool_call.arguments}")
result = await self.tool_registry.execute(tool_call.name, tool_call.arguments)
print(f"   结果: {result[:200]}...")
```

运行 `nanobot agent`，问一个需要用工具的问题，观察输出。

**实验2：查看完整 System Prompt**

在 `agent/context.py` 的 `build_system()` 方法末尾添加：

```python
print("\n" + "="*60)
print("SYSTEM PROMPT（AI看到的内容）：")
print("="*60)
print(system[:2000])  # 打印前2000字符
print("="*60 + "\n")
```

运行后，你会看到 AI 每次对话时真正看到什么。

**实验3：修改工具截断长度**

找到 `agent/loop.py` 顶部的：

```python
_TOOL_RESULT_MAX_CHARS = 500
```

改成 `50`（非常短），运行后问一个需要读长文件的问题，观察截断效果。

**实验4：创建自定义工具**

在 `agent/tools/` 下创建 `my_tool.py`：

```python
from nanobot.agent.tools.base import Tool

class HelloTool(Tool):
    @property
    def name(self) -> str:
        return "hello"

    @property
    def description(self) -> str:
        return "打招呼工具，向指定名字的人问好"

    def get_parameters(self) -> dict:
        return {
            "type": "object",
            "properties": {
                "name": {"type": "string", "description": "要问好的人的名字"}
            },
            "required": ["name"]
        }

    async def execute(self, name: str) -> str:
        return f"你好，{name}！很高兴认识你！"
```

然后在 `agent/loop.py` 的 `_register_default_tools()` 方法中注册：

```python
from nanobot.agent.tools.my_tool import HelloTool
self.tool_registry.register(HelloTool())
```

重启后，AI 就多了一个 `hello` 工具！

**实验5：添加新的 SKILL.md**

```bash
mkdir -p ~/.nanobot/workspace/skills/haiku
```

创建 `~/.nanobot/workspace/skills/haiku/SKILL.md`：

```markdown
---
name: haiku
description: 用中文写俳句（5-7-5音节）
always: false
available: true
---

# 俳句技能

## 俳句规则
- 三行诗，格式：5音节 + 7音节 + 5音节
- 要有季节意象（季语）
- 用中文创作

## 示例
春雨悄悄落，
樱花随风飘散去，
归鸟啼声远。
```

重启后：

```
You: 给我写一首关于月亮的俳句
AI（读取了 haiku SKILL.md）：
皓月当空悬，
清辉洒满静夜庭，
独影对长天。
```

---

*本教程基于 nanobot v0.1.4.post4（2026-03-11）编写，覆盖项目全部 62 个 Python 文件和 27 个测试文件。*

*文档版本：超详细小白版 v2.0*
