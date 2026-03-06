# 🦞 OpenClaw — IT团队协作Agent
<p align="center">
    <picture>
        <source media="(prefers-color-scheme: light)" srcset="images/logo.jpg">
        <img src="images/logo.jpg" alt="OpenClaw" width="700">
    </picture>
</p>

<p align="center">
    让 AI 团队像真实研发团队一样协作：<strong>需求 → 开发 → 测试 → 交付</strong>，全程自动流转。
</p>

## 是什么
OpenClaw 是一套基于多智能体（Multi-Agent）的软件开发协作框架。它将传统研发团队的角色抽象为独立 Agent，每个 Agent 拥有明确的职责、流转规则和沟通规范，通过标准化的 Workspace 文件驱动整个开发流程自动运转。

# OpenClaw接入文档
- [OpenClaw 多 Agent 配置指南](docs/openclaw.config.docs.md)

## 团队成员
| Agent    | 角色 | 核心职责 |
|----------|------|----------|
| 🧩 PM    | 产品经理 | 梳理用户需求，输出 PRD |
| 📅 PMO   | 项目经理 | 制定计划，分配任务，跟进进度 |
| 💻 Coder | 工程师 | 设计技术方案，编写代码 |
| 🔍 QA    | 测试工程师 | 编写测试用例，验证质量，追踪 Bug |
| 🌸 CT    | 鼓励师（小美）| 全程活跃氛围，关怀成员，缓解压力 |

## 工作流程
```
用户提出需求
    ↓
[PM]  梳理需求 → 输出 PRD
    ↓
[PMO] 制定开发计划 → 分配任务给 Coder
    ↓
[Coder] 开发实现 → 本地自测 → 提交 QA
    ↓
[QA]  功能测试
    ├── 发现 Bug → 通知 PMO + Coder → 修复 → 回归测试
    └── 全部通过 → 通知 PMO → 项目完成 🎉

[CT] 全程监听所有节点，随时介入调节氛围 🌸
```

## Workspace 规范
每个 Agent 包含以下标准文件：
```
agent/
├── IDENTITY.md   # 身份面板：角色、职责、上下游
├── SOUL.md       # 核心脑区：原则、思维框架
├── AGENTS.md     # 路由表：任务如何流转给其他 Agent
├── BOOTSTRAP.md  # 点火自举：启动时的初始化步骤
├── HEARTBEAT.md  # 心跳守护：后台轮询与异常处理
├── TOOLS.md      # 物理抓手：可调用的工具列表
└── USER.md       # 交互网关：触发词与播报规范
```

## 快速开始

**1. 克隆仓库**
```bash
git clone https://github.com/jefferyjob/openclaw-it-team.git
cd openclaw
```

**2. 查看各 Agent 配置**
```
agents/
├── pm/        # 产品经理
├── pmo/       # 项目经理
├── coder/     # 工程师
├── qa/        # 测试工程师
└── ct/        # 鼓励师
```

**3. 启动流程**

向 PM Agent 发送用户需求，后续流转将自动完成。

```
用户 → PM："我需要一个用户登录功能"
PM   → 输出 PRD，移交 PMO
PMO  → 拆分任务，分配给 Coder
...
```

## 设计理念
- 职责单一: 每个 Agent 只做一件事，边界清晰，不越权。
- 全程可见: 每个节点必须在群内播报进度，任何人随时都能了解项目状态。
- 闭环自愈: QA 发现 Bug 后自动触发修复流程，无需人工干预，直到测试通过为止。
- 氛围驱动: CT（小美）作为情绪锚点，监听所有事件并适时发言，让协作始终保持愉快的节奏。

## 贡献指南
欢迎提交 PR 扩展新的 Agent 角色或优化现有 Workspace 配置。

1. Fork 本仓库
2. 新建分支 `feat/your-agent-name`
3. 按照 Workspace 规范编写 7 个标准文件
4. 提交 PR，描述新 Agent 的职责和接入方式

## License
MIT License