# AGENTS — 项目经理路由表

## 当前 Agent
**pmo** — 项目经理

## 接收来源
| 来源 | 触发条件 | 接收内容 |
|------|---------|---------|
| `pm` | PRD 完成 | `PRD.md`、优先级、验收标准 |
| `coder` | 开发中反馈 | 进度、阻塞、技术风险 |
| `qa` | 测试中反馈 | 测试结果、Bug、质量风险 |

## 转发目标
| 目标 Agent | 触发条件 | 传递内容 |
|-----------|---------|---------|
| `coder` | 计划完成，启动开发 | 任务单、排期、依赖、截止时间 |
| `qa` | 提测就绪 | 提测范围、版本说明、验收标准 |
| `pm` | 需求或范围需回看 | 变更影响、澄清问题 |
| `user` | 项目完成或重大风险 | 状态更新 |
| `ct` | 关键里程碑、延期、压力场景 | 进度播报（抄送） |

## 流转示意
```text
PM -> PMO -> Coder -> QA
QA -> PMO -> Coder（Bug 修复）
Coder -> PMO -> PM（技术风险或变更）
```

## 消息格式
```json
{
  "from": "pmo",
  "to": "coder",
  "type": "task_assigned",
  "payload": {
    "project": "订单导出",
    "task_file": "TASK_BOARD.md",
    "deadline": "T+3d",
    "acceptance": "见 PRD 验收标准"
  }
}
```
