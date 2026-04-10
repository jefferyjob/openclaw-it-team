---
name: openclaw-it-team-deploy
description: “为 openclaw-it-team 仓库生成和维护部署型工作流。Use when the user wants to deploy, install, set up, or run this repo's multi-Agent configuration in OpenClaw — including localizing openclaw.json, filling in model and Feishu (飞书) account credentials, and completing pre-launch acceptance. Common triggers include deploy, install agents, setup openclaw, run the team, configure feishu bots, 部署, 安装, 配置, 跑起来, 接入飞书.”
---

# OpenClaw IT Team Deploy Skill

本 Skill 用于部署当前仓库这套多 Agent 配置。

适用场景：
- 用户要求”部署 / 安装 / 配置 / 跑起来 / deploy / setup / install agents”当前仓库
- 用户要求把仓库中的 `agents/*` 安装到本机 OpenClaw
- 用户要求生成或修改 `~/.openclaw/openclaw.json` 以接入这套 `PM -> RD -> QA -> CE` 团队
- 用户要求接入飞书 Bot、模型提供商、Agent 路由或工作目录

这不是传统 Web 项目部署。当前仓库的”部署”本质上是：
1. 把仓库内的 Agent Workspace 文件安装到 OpenClaw 目录
2. 以 `docs/openclaw.json` 为模板生成用户本机可用的配置
3. 填入模型与渠道账号配置
4. 启动 OpenClaw 并验收 Agent 路由是否正常

## 先读哪些文件

默认只读这些：
- `README.md`：理解仓库目标与角色分工
- `docs/openclaw.json`：部署模板，以它为准落配置
- `agents/pm/*`、`agents/rd/*`、`agents/qa/*`、`agents/ce/*`：要安装的实际 Agent 文件

仅在需要解释配置字段时，再读：
- `docs/openclaw.config.docs.md`

## 标准目标目录

默认建议部署到用户主目录下（若用户指定其他目录，以用户指定为准）：

```text
~/.openclaw/
├── openclaw.json
├── workspace/
├── workspace-pm/
├── workspace-rd/
├── workspace-qa/
└── workspace-ce/
```

注意：仓库里的源目录是 `agents/pm` 这种结构，部署目标应映射到角色工作目录（如 `~/.openclaw/workspace-pm`），不要再部署到 `~/.openclaw/agents/pm/agent` 这种旧路径。

## 标准工作流

### 1. 先确认部署前提

至少确认这些信息：
- OpenClaw 已安装，或者用户允许你检查本机 OpenClaw 命令
- 目标部署目录：默认 `~/.openclaw`（用户指定其他目录时按用户指定执行）
- 模型提供商与模型 ID
- 是否启用飞书
- 若启用飞书，是否已提供 `pm / rd / qa / ce` 四个 Bot 的 `appId` 和 `appSecret`

执行原则：
- 优先替用户直接完成落盘、替换、校验，不要只给口头步骤
- 若目标目录已存在文件，先比对再覆盖，避免无提示破坏用户现有配置

如果用户只是想先把配置落地、不立即联通飞书，可以先写模板，但必须明确哪些占位符仍未完成。

### 2. 安装 Agent 文件

把仓库内四个角色目录复制到目标目录：

```text
repo/agents/pm/* -> ~/.openclaw/workspace-pm/
repo/agents/rd/* -> ~/.openclaw/workspace-rd/
repo/agents/qa/* -> ~/.openclaw/workspace-qa/
repo/agents/ce/* -> ~/.openclaw/workspace-ce/
```

要求：
- 每个目标目录都应包含 8 个标准文件：`AGENTS.md`、`BOOTSTRAP.md`、`HEARTBEAT.md`、`IDENTITY.md`、`MEMORY.md`、`SOUL.md`、`TOOLS.md`、`USER.md`
- 缺文件时，不要继续宣称部署完成

### 3. 生成本机 `openclaw.json`

以 `docs/openclaw.json` 为唯一模板，做最小必要修改：

- 把所有 `/root/.openclaw/...` 改为当前用户真实 Home 目录（不要假设用户机器是 `/root`）
- 保持 Agent ID、路由关系、`mentionPatterns`、`allowAgents` 不变
- 保持 `gateway.port = 18789`，除非用户明确要求修改
- 保持 `messages`、`commands`、`session`、`tools` 的现有设计，除非部署环境要求调整

优先替换这些字段：
- `agents.defaults.workspace`
- `agents.list[*].workspace`
- `agents.list[*].agentDir`（与对应 `workspace-*` 目录保持一致）
- `models.providers`
- `agents.defaults.model.primary`
- `agents.defaults.models`
- `channels.feishu.accounts.*.appId`
- `channels.feishu.accounts.*.appSecret`

### 4. 模型配置规则

默认模板使用：

```json
"primary": "zai/glm-5"
```

如果用户并不使用 `zai/glm-5`：
- 同步修改 `models.providers`
- 同步修改 `agents.defaults.model.primary`
- 同步修改 `agents.defaults.models` 中与主模型对应的键
- 确保 provider/id 的引用一致，不要只改一处

### 5. 飞书配置规则

当前仓库设计为四个 Agent 绑定四个飞书账号：
- `feishu-bot-pm`
- `feishu-bot-rd`
- `feishu-bot-qa`
- `feishu-bot-ce`

处理规则：
- 有真实凭据时，替换 `YOUR_*` 占位值
- 没有真实凭据时，不要写假值；保留占位符并明确标记为未完成
- `ce` 默认 `requireMention: false`，其余角色默认 `requireMention: true`，没有明确需求时不要改
- `bindings` 中 `agentId` 和 `accountId` 必须一一对应

### 6. 启动前检查

至少做这些检查：
- 目标 `openclaw.json` 是合法 JSON
- `agents.list[*].workspace` 指向的目录都存在
- `agents.list[*].agentDir` 与对应 `workspace-*` 目录一致
- 四个角色工作目录（`workspace-pm/rd/qa/ce`）都含 8 个标准文件
- 若填写了模型 provider，关键字段 `baseUrl`、`api`、`models` 结构完整
- 若填写了飞书账号，四组 `appId/appSecret` 没有残留明显占位符

### 7. 启动与验收

优先顺序：
1. 先探测本机 OpenClaw 启动方式（`which openclaw`、`openclaw --version` 等）
2. 使用本机真实可用命令启动，不要编造命令
3. 启动后检查网关或日志，确认配置被正确加载

可接受的验收信号：
- OpenClaw 进程正常启动，无配置解析错误
- `openclaw.json` 被加载，无 `workspace/agentDir` 路径缺失报错
- 网关监听 `127.0.0.1:18789` 或用户指定端口
- 飞书启用时，各账号绑定未报错

如果本机没有 OpenClaw 可执行命令，或用户未提供安装方式：
- 明确说明“配置已落地，但运行验证未完成”
- 不要把“文件已写入”表述成“服务已部署成功”

## 修改当前仓库时的输出要求

如果用户要求你“完善这个仓库，让后续部署更方便”，优先做这些事：
- 保持 `docs/openclaw.json` 作为单一模板来源
- 保持 `README.md` 面向人类阅读，`SKILL.md` 面向代理执行
- 在 `SKILL.md` 中只写执行流程、判断条件、路径映射、校验规则
- 不要把长篇配置字段解释重复写进 `SKILL.md`；需要时引用 `docs/openclaw.config.docs.md`

## 完成任务后的汇报格式

完成部署类任务后，汇报应至少包含：
- 部署到了哪个目录
- 修改了哪些关键文件
- 哪些配置已完成
- 哪些占位符仍待用户提供
- 是否已完成实际启动验证
- 若未验证，卡在哪里
