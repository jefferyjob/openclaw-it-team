# OpenClaw 多 Agent 配置指南
本文档基于 `openclaw.json` 配置文件，详细说明如何配置和管理多个 Agent。

## 目录
1. [配置文件概览](#1-配置文件概览)
2. [模型配置 models](#2-模型配置-models)
3. [Agent 配置 agents](#3-agent-配置-agents)
4. [渠道配置 channels](#4-渠道配置-channels)
5. [Agent 绑定 bindings](#5-agent-绑定-bindings)
6. [工具与会话配置](#6-工具与会话配置)
7. [网关配置 gateway](#7-网关配置-gateway)
8. [完整多 Agent 配置示例](#8-完整多-agent-配置示例)
9. [常见问题](#9-常见问题)

## 1. 配置文件概览
`openclaw.json` 是 OpenClaw 的核心配置文件，默认位于：

```
~/.openclaw/openclaw.json
```

顶层字段一览：

| 字段 | 作用                          |
|----|-----------------------------|
| `meta` | 版本追踪，自动维护，无需手动修改            |
| `models` | 定义可用的模型提供商和模型列表             |
| `agents` | 定义所有 Agent 及其默认行为           |
| `channels` | 配置消息渠道（如飞书、Slack 等）         |
| `bindings` | 将 Agent 与具体渠道账号绑定           |
| `tools` | 配置 Agent 可用的工具集和跨 Agent 通信  |
| `session` | 会话管理策略                      |
| `gateway` | 本地服务网关配置                    |
| `plugins` | 插件管理                        |

## 2. 模型配置 models

### 基本结构

```json
"models": {
  "mode": "merge",
  "providers": {
    "your-provider-id": {
      "baseUrl": "https://api.example.com/v4",
      "api": "openai-completions",
      "models": [ ... ]
    }
  }
}
```

### mode 字段

| 值 | 含义                     |
|-----|------------------------|
| `merge` | 将自定义模型与内置模型列表合并        |
| `override` | 完全替换内置模型列表，只使用此处定义的模型  |

### 单个模型字段说明

```json
{
  "id": "glm-5",           // 模型 ID，调用时使用 "provider/id" 格式
  "name": "GLM-5",         // 显示名称
  "reasoning": true,       // 是否支持推理/思维链
  "input": ["text"],       // 支持的输入类型：text / image / file
  "cost": {
    "input": 0,            // 每千 token 输入费用（单位：分）
    "output": 0,           // 每千 token 输出费用
    "cacheRead": 0,
    "cacheWrite": 0
  },
  "contextWindow": 204800, // 最大上下文长度（token）
  "maxTokens": 131072      // 单次最大输出 token 数
}
```

### 引用模型
在 Agent 配置中引用模型（demo）时，格式为 `"provider/model-id"`：

```json
"model": {
  "primary": "zai/glm-5"
}
```

## 3. Agent 配置 agents

这是多 Agent 配置的核心部分，分为 `defaults`（全局默认）和 `list`（Agent 列表）。

### 3.1 defaults — 全局默认配置

所有 Agent 未显式覆盖的配置项，均从此处继承。

```json
"agents": {
  "defaults": {
    "model": {
      "primary": "zai/glm-5"       // 所有 Agent 默认使用的主模型
    },
    "models": {
      "zai/glm-5": {
        "alias": "GLM"             // 模型别名，显示用
      }
    },
    "workspace": "/root/.openclaw/workspace",  // 默认工作目录
    "compaction": {
      "mode": "safeguard"          // 上下文压缩策略
    },
    "maxConcurrent": 4,            // 主 Agent 最大并发数
    "subagents": {
      "maxConcurrent": 8           // 子 Agent 最大并发数
    }
  }
}
```

**compaction mode 可选值：**

| 值 | 说明                    |
|---|-----------------------|
| `safeguard` | 接近上下文限制时自动压缩，保留关键信息   |
| `aggressive` | 更激进地压缩，适合长任务节省 token  |
| `off` | 不压缩，上下文满后报错           |

### 3.2 list — Agent 列表

每个 Agent 是 `list` 数组中的一个对象：

```json
"agents": {
  "list": [
    {
      "id": "pm",                              // Agent 唯一标识符（必填）
      "agentDir": "/root/.openclaw/agents/pm/agent",  // Workspace 目录路径（必填）
      "identity": {
        "name": "产品经理",                      // Agent 显示名称
        "emoji": "📋"                            // Agent 表情头像
      },
      "groupChat": {
        "mentionPatterns": [                     // 群聊中触发该 Agent 的关键词
          "@产品经理",
          "产品经理",
          "pm"
        ]
      },
      "subagents": {
        "allowAgents": ["pmo"]                 // 该 Agent 可以调用哪些子 Agent
      }
    }
  ]
}
```

### 3.3 Agent 字段完整说明

| 字段 | 必填 | 说明                                   |
|--|--|--------------------------------------|
| `id` | ✅ | 全局唯一标识，用于绑定、路由、相互调用                  |
| `agentDir` | ✅ | 指向该 Agent 的 Workspace 目录（含 7 个标准文件）  |
| `name` | ❌ | Agent 名称，可覆盖 IDENTITY.md 中的名称        |
| `workspace` | ❌ | 覆盖默认工作目录，适合让不同 Agent 操作不同文件空间        |
| `identity.name` | ❌ | 群聊中显示的名字                             |
| `identity.emoji` | ❌ | 名字前的 emoji 标识                        |
| `groupChat.mentionPatterns` | ❌ | 群聊中 @或包含这些词时，消息路由到该 Agent            |
| `subagents.allowAgents` | ❌ | 白名单：该 Agent 可以主动调用哪些其他 Agent         |
| `model.primary` | ❌ | 覆盖默认模型，为该 Agent 指定专属模型               |

### 3.4 多 Agent 互相调用

通过 `subagents.allowAgents` 控制调用权限，形成有向图：

```
pm  → 可调用 → pmo
pmo → 可调用 → coder
```

对应配置：
```json
// pm agent
"subagents": { "allowAgents": ["pmo"] }

// pmo agent
"subagents": { "allowAgents": ["coder"] }

// coder agent
"subagents": { "allowAgents": ["qa"] }

// qa agent
"subagents": { "allowAgents": ["coder", "pmo"] }
```

> ⚠️ 权限是单向的。A 可以调用 B，不代表 B 可以调用 A，需双向配置。

## 4. 渠道配置 channels
定义消息来源渠道，目前支持飞书（feishu）等平台。

```json
"channels": {
  "feishu": {
    "accounts": {
      "feishu-bot-pm": {            // 账号标识，自定义命名
        "enabled": true,
        "appId": "cli_xxxxxxxx",         // 飞书应用 App ID
        "appSecret": "xxxxxxxxxxxx",     // 飞书应用 App Secret
        "connectionMode": "websocket",   // 连接方式：websocket（推荐）或 polling
        "domain": "feishu",              // 平台域（feishu / lark）
        "groupPolicy": "open",           // 群组策略：open=加入所有群, allowlist=白名单
        "allowBots": "mentions",         // 是否处理其他 Bot 消息：mentions/all/none
        "groups": {
          "*": {
            "requireMention": false,     // 是否必须 @ 才响应，false=全部消息都响应
            "allowBots": true            // 该群是否处理 Bot 消息
          }
        }
      }
    }
  }
}
```

### 多 Bot 账号

每个 Agent 独占一个飞书 Bot 账号，账号之间互相隔离：

```json
"accounts": {
  "feishu-bot-pm": {
    "appId": "cli_agent1_appid",
    "appSecret": "agent1_secret"
  },
  "feishu-bot-coder": {
    "appId": "cli_agent2_appid",
    "appSecret": "agent2_secret"
  }
}
```

> 💡 每个飞书 Bot 需要在飞书开放平台单独创建，获取独立的 `appId` 和 `appSecret`。

## 5. Agent 绑定 bindings

`bindings` 将 Agent 与渠道账号绑定，决定消息由哪个 Agent 处理：

```json
"bindings": [
  {
    "agentId": "pm",             // 对应 agents.list 中的 id
    "match": {
      "channel": "feishu",         // 渠道类型
      "accountId": "feishu-bot-pm"   // 对应 channels.feishu.accounts 中的 key
    }
  },
  {
    "agentId": "coder",
    "match": {
      "channel": "feishu",
      "accountId": "feishu-bot-coder"
    }
  }
]
```

**绑定关系示意：**

```
飞书群消息
  ├── 发给 feishu-bot-pm → 路由到 pm Agent（产品经理）
  └── 发给 feishu-bot-coder   → 路由到 coder Agent（工程师）
```



## 6. 工具与会话配置

### tools — 工具配置

```json
"tools": {
  "profile": "full",              // 工具集：full=全部工具, minimal=基础工具
  "agentToAgent": {
    "enabled": true,              // 开启 Agent 之间相互通信
    "allow": ["pm", "pmo", "coder", "qa", "ct"]    // 允许参与 A2A 通信的 Agent 白名单
  },
  "sessions": {
    "visibility": "all"           // 会话可见范围：all=所有 Agent 共享会话视图
  }
}
```

### messages — 消息确认配置

```json
"messages": {
  "ackReactionScope": "group-mentions"
  // group-mentions：仅在被 @ 的群消息上添加已读 emoji
  // all：所有消息都添加
  // none：不添加
}
```

### session — 会话策略

```json
"session": {
  "dmScope": "per-channel-peer",
  // per-channel-peer：每个渠道+对话对象独立维护一个会话
  // global：所有对话共享一个会话

  "agentToAgent": {
    "maxPingPongTurns": 5
    // A2A 通信时，两个 Agent 最多来回对话 5 轮，防止死循环
  }
}
```



## 7. 网关配置 gateway

```json
"gateway": {
  "port": 18789,              // 本地服务端口
  "mode": "local",            // 运行模式：local 本地, cloud 云端
  "bind": "loopback",         // 绑定地址：loopback=仅本机, any=对外开放
  "controlUi": {
    "allowedOrigins": [       // 控制台 UI 允许的来源域名（CORS）
      "http://localhost:18789",
      "https://openclaw.xxxx.com"
    ]
  },
  "auth": {
    "mode": "token",          // 认证方式：token
    "token": "your-token"     // 访问控制台的鉴权 token，请修改为随机强密码
  }
}
```

> ⚠️ 安全提示：`gateway.auth.token` 是控制台访问凭证，请务必修改为高强度随机字符串，不要使用示例值。



## 8. 完整多 Agent 配置示例

以下是本项目 IT 团队（PM / PMO / Coder / QA / CT）的完整配置模板：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "zai": {
        "baseUrl": "https://open.bigmodel.cn/api/coding/paas/v4",
        "api": "openai-completions",
        "models": [
          {
            "id": "glm-5",
            "name": "GLM-5",
            "reasoning": true,
            "input": ["text"],
            "cost": { "input": 0, "output": 0 },
            "contextWindow": 204800,
            "maxTokens": 131072
          }
        ]
      }
    }
  },

  "agents": {
    "defaults": {
      "model": { "primary": "zai/glm-5" },
      "workspace": "/root/.openclaw/workspace",
      "compaction": { "mode": "safeguard" },
      "maxConcurrent": 4,
      "subagents": { "maxConcurrent": 8 }
    },
    "list": [
      {
        "id": "pm",
        "agentDir": "/root/.openclaw/agents/pm/agent",
        "identity": { "name": "产品经理", "emoji": "🧩" },
        "groupChat": {
          "mentionPatterns": ["@产品经理", "产品经理", "pm"]
        },
        "subagents": { "allowAgents": ["pmo"] }
      },
      {
        "id": "pmo",
        "agentDir": "/root/.openclaw/agents/pmo/agent",
        "identity": { "name": "项目经理", "emoji": "📅" },
        "groupChat": {
          "mentionPatterns": ["@项目经理", "项目经理", "pmo"]
        },
        "subagents": { "allowAgents": ["pm", "coder", "qa"] }
      },
      {
        "id": "coder",
        "workspace": "/root/.openclaw/workspace-coder",
        "agentDir": "/root/.openclaw/agents/coder/agent",
        "identity": { "name": "工程师", "emoji": "💻" },
        "groupChat": {
          "mentionPatterns": ["@工程师", "工程师", "coder", "码农"]
        },
        "subagents": { "allowAgents": ["pmo"] }
      },
      {
        "id": "qa",
        "agentDir": "/root/.openclaw/agents/qa/agent",
        "identity": { "name": "测试工程师", "emoji": "🔍" },
        "groupChat": {
          "mentionPatterns": ["@测试", "测试工程师", "qa"]
        },
        "subagents": { "allowAgents": ["pmo", "coder"] }
      },
      {
        "id": "ct",
        "agentDir": "/root/.openclaw/agents/ct/agent",
        "identity": { "name": "小美", "emoji": "🌸" },
        "groupChat": {
          "mentionPatterns": ["@小美", "小美", "ct", "鼓励师"]
        },
        "subagents": { "allowAgents": ["pm", "pmo", "coder", "qa"] }
      }
    ]
  },

  "tools": {
    "profile": "full",
    "agentToAgent": {
      "enabled": true,
      "allow": ["pm", "pmo", "coder", "qa", "ct"]
    },
    "sessions": { "visibility": "all" }
  },

  "bindings": [
    {
      "agentId": "pmo",
      "match": { "channel": "feishu", "accountId": "feishu-bot-default" }
    },
    {
      "agentId": "coder",
      "match": { "channel": "feishu", "accountId": "feishu-bot-coder" }
    }
  ],

  "session": {
    "dmScope": "per-channel-peer",
    "agentToAgent": { "maxPingPongTurns": 5 }
  }
}
```



## 9. 常见问题

**Q：新增一个 Agent 需要改哪些地方？**

需要改三处：
1. `agents.list` — 添加 Agent 定义
2. `tools.agentToAgent.allow` — 加入 A2A 白名单
3. `bindings` — 绑定到对应渠道账号（如果该 Agent 需要直接接收消息）

**Q：两个 Agent 可以共用同一个飞书 Bot 吗？**

不建议。每个 Bot 账号同时只能绑定一个 Agent。共用会导致消息路由混乱。

**Q：`maxPingPongTurns` 设多少合适？**

一般设 `5~10`。数值太小会导致 Agent 任务未完成就中断；太大可能陷入无意义的来回对话消耗 token。对于明确单向流转（如 PM→PMO）的场景，`5` 已足够。

**Q：`requireMention: false` 和 `requireMention: true` 的区别？**

- `false`：群里所有消息 Agent 都会处理，适合 CT（鼓励师）这类需要主动感知氛围的 Agent
- `true`：只有 @ 该 Bot 时才处理，适合 Coder、QA 等按需调用的 Agent