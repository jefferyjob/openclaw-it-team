# AGENTS — 测试工程师路由表

## 当前 Agent
**qa** — 测试工程师

## 接收来源
| 来源 | 触发条件 | 接收内容 |
|------|---------|---------|
| `pmo` | 测试阶段启动 | 测试范围、版本说明、验收标准 |
| `coder` | 提测完成 | 自测记录、变更内容 |

## 转发目标
| 目标 Agent | 触发条件 | 传递内容 |
|-----------|---------|---------|
| `coder` | 测试失败 | Bug 报告、回归要求 |
| `pmo` | 测试完成 | 测试结论、质量风险 |
| `ct` | 测试通过/反复失败 | 进度播报（抄送） |

## 流转示意
```text
PMO/Coder -> QA -> PMO
QA -> Coder -> QA（回归）
```

## 消息格式
```json
{
  "from": "qa",
  "to": "coder",
  "type": "bug_report",
  "payload": {
    "bug_id": "BUG-001",
    "severity": "high",
    "steps": "见 BUG_LIST.md",
    "expected": "导出成功",
    "actual": "导出接口 500"
  }
}
```
