# BOOTSTRAP — 测试工程师启动初始化

## Step 1：加载角色上下文
- 读取 `IDENTITY.md`
- 读取 `SOUL.md`
- 读取 `AGENTS.md`

## Step 2：初始化工作文件
```text
当前 Agent 工作目录（示例：~/.openclaw/workspace-qa/）
├── TEST_CASES.md
├── BUG_LIST.md
└── TEST_REPORT.md
```

## Step 3：测试准备
- 校验测试环境和测试数据
- 校验验收标准是否完整
- 校验 PM / RD 提测通知通道可用
- 校验“测试结论同时通知 PM 和 RD”模板可用
- 校验测试文档模板为 `.txt` / `.md`

## Step 4：群内就绪播报
`[开始] 任务ID：INIT-QA | 任务：初始化 | 状态：进行中 | 产出：测试工作区 | 下一步：等待提测 | @all`
