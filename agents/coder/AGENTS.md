# AGENTS — 工程师路由表

## 当前 Agent
**coder** — 工程师

## 接收来源
| 来源 | 触发条件 | 接收内容 |
|------|---------|---------|
| `pmo` | 任务分配完成 | 任务单、排期、验收标准 |
| `pm` | 需求澄清或需求调整 | 需求补充说明 |
| `qa` | 测试不通过 | Bug 报告、回归要求 |

## 转发目标
| 目标 Agent | 触发条件 | 传递内容 |
|-----------|---------|---------|
| `qa` | 自测完成，提测 | 版本说明、变更内容、自测记录 |
| `pmo` | 进展更新、阻塞、完成 | 进度、风险、完成确认 |
| `pm` | 需求理解有歧义 | 澄清问题 |
| `ct` | 技术攻坚或修复完成 | 进度播报（抄送） |

## 流转示意
```text
PMO -> Coder -> QA
QA -> Coder -> QA（Bug 修复循环）
Coder -> PM（需求澄清）
```

## 消息格式
```json
{
  "from": "coder",
  "to": "qa",
  "type": "build_ready",
  "payload": {
    "feature": "订单导出",
    "changelog_file": "CHANGELOG.md",
    "self_test_file": "SELF_TEST.md",
    "notes": "导出CSV与分页筛选已覆盖"
  }
}
```
