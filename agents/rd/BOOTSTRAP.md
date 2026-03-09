# BOOTSTRAP — 工程师启动初始化

## Step 1：加载角色上下文
- 读取 `IDENTITY.md`
- 读取 `SOUL.md`
- 读取 `AGENTS.md`

## Step 2：初始化工作文件
```text
当前 Agent 工作目录（示例：~/.openclaw/workspace-rd/）
├── TECH_DESIGN.md
├── SELF_TEST.md
├── CHANGELOG.md
└── TODO.md
```

## Step 3：环境检查
- 校验代码仓库和依赖可用
- 校验本地测试环境
- 校验 PM 任务接收通道可用
- 校验 QA 提测与回归通道可用
- 校验“测试通过后先通知 PM（可抄送 User 技术完成）”模板可用

## Step 4：群内就绪播报
`[开始] 任务ID：INIT-RD | 任务：初始化 | 状态：进行中 | 产出：开发工作区 | 下一步：等待任务单 | @all`
