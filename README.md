# 🦞OpenClaw IT Team Workspace

<p align="center">
  <img src="images/logo.jpg" alt="OpenClaw" width="700">
</p>

<p align="center">
  Simulate a complete IT team with a multi-agent workflow, automatically driving the full process from <strong>requirements → development → testing → delivery</strong>.
</p>

English | [简体中文](README.cn.md)

## Project Overview
🦞OpenClaw IT Team is a multi-agent collaboration setup designed for software development scenarios. Its goal is not to have agents answer questions side by side, but to let them operate like a real team: hand off work by role, collaborate across stages, and continuously sync progress in a shared group.

## ✨ Quick Start

### Option 1: Let Lobster deploy it for you (recommended for OpenClaw users)
If you are already using OpenClaw, just send the following message to your Lobster:
```text
Please help me deploy openclaw-it-team according to this SKILL.md:
https://github.com/jefferyjob/openclaw-it-team/blob/main/SKILL.md
```

**⚠️ Important:**
- If you are **not** an **OpenClaw** user, it is recommended to use **Option 2** directly because the steps are simpler.
- After deployment, you need to **manually edit the `openclaw.json` configuration file** and fill in your own **Feishu `AppId` and `AppSecret`** before it can work properly.
- Also make sure your **Feishu Bot** has the required **application permissions**, otherwise the bot may not function correctly.

### Option 2: Manual deployment
#### 1. Clone the repository
```bash
git clone https://github.com/jefferyjob/openclaw-it-team.git
cd openclaw-it-team
```

#### 2. Review the final solution
- Full team workflow
- Unified group broadcast protocol
- Complete Workspace definitions for each Agent
- Exception handling rules

#### 3. Review the actual configuration for each role

```text
agents/
├── pm/
├── rd/
├── qa/
└── ce/
```

For example:

- [`agents/pm/IDENTITY.md`](agents/pm/IDENTITY.md)
- [`agents/pm/AGENTS.md`](agents/pm/AGENTS.md)
- [`agents/rd/MEMORY.md`](agents/rd/MEMORY.md)
- [`agents/qa/SOUL.md`](agents/qa/SOUL.md)
- [`agents/ce/USER.md`](agents/ce/USER.md)

### Quick experience: start a complete workflow from PM

A typical entry point is that the user gives a requirement to `pm`, for example:

```text
I need a user login feature that supports SMS verification code login.
```

The standard workflow after that is:

```text
PM -> outputs PRD and plan
RD -> develops and submits for testing
QA -> tests and gives feedback
PM -> closes the project and syncs with the user
CE -> supports the atmosphere throughout the process
```

## Core Features
- Single owner: `PM` is fully responsible for requirements and project delivery, reducing the cost of role switching.
- Closed-loop workflow: the standard process is fixed as `PM -> RD -> QA`. If testing fails, work returns to RD for fixes, and PM drives the loop to closure.
- Full visibility: all roles are required to post updates to the group at the start, during progress, and at completion.
- Document-driven: each Agent is defined through standard Workspace files covering identity, routing, tools, and interaction rules.
- Emotional coordination: `CE` listens throughout the process and helps users and the team maintain a stable communication rhythm.

## Recommended Reading Order
If this is your first time exploring this repository, it is recommended to read in this order:

1. [`docs/openclaw.config.docs.md`](docs/openclaw.config.docs.md)
   OpenClaw configuration documentation, useful for understanding the underlying integration approach.
2. `agents/*`
   The actual Workspace files for each role.

## Agent Workspace Structure

Each Agent directory in the repository (such as `agents/pm/`) currently contains 8 standard files:

```text
agents/<role>/
├── AGENTS.md
├── BOOTSTRAP.md
├── HEARTBEAT.md
├── IDENTITY.md
├── MEMORY.md
├── SOUL.md
├── TOOLS.md
└── USER.md
```

Deployment mapping (consistent with `docs/openclaw.json`):

```text
repo/agents/pm/* -> ~/.openclaw/workspace-pm/
repo/agents/rd/* -> ~/.openclaw/workspace-rd/
repo/agents/qa/* -> ~/.openclaw/workspace-qa/
repo/agents/ce/* -> ~/.openclaw/workspace-ce/
```

Description:
- `IDENTITY.md`: role identity, responsibilities, upstream/downstream, deliverables, and prohibited behaviors
- `SOUL.md`: role mindset, workflow rules, and group broadcast format
- `AGENTS.md`: task sources, forwarding targets, and message structure
- `BOOTSTRAP.md`: startup initialization steps and workspace files
- `HEARTBEAT.md`: scheduled inspection, exception triggers, and fallback actions
- `TOOLS.md`: the physical tools available to the role and usage rules
- `USER.md`: trigger phrases and response rules from users or group chats
- `MEMORY.md`: long-term agreements, terminology, historical experience, and collaboration reminders

## Agent Roles

| Agent | Role | Main Responsibilities | Key Outputs |
|------|------|---------|---------|
| 🧩 PM | Project Manager | Requirement clarification, PRD, scheduling, task assignment, risk management, project closure | `PRD.md`, `TASK_BOARD.md`, `RISK_LOG.md`, delivery status |
| 💻 RD | Engineer | Technical design, implementation, self-testing, bug fixing | Code, technical solution, self-test records |
| 🔍 QA | Test Engineer | Functional testing, regression testing, quality assurance | Test reports, bug list |
| 🌸 CE | Cheerleader | Comfort users, regulate atmosphere, relieve pressure | Group atmosphere updates, emotional reminders |

## Standard Workflow

```text
User submits a requirement
  ->
PM clarifies requirements, produces PRD, makes a plan, and assigns tasks
  ->
RD develops, self-tests, and submits for testing
  ->
QA tests
  -> Pass: PM announces completion and syncs with the user
  -> Fail: QA reports bugs -> RD fixes -> QA regresses

CE actively speaks at each stage to encourage, comfort, and celebrate when appropriate.
```

## Group Collaboration Rules
This is one of the most important collaboration constraints in the current repository:

- Each Agent must announce when starting a task
- Each Agent must announce key progress or blockers
- Each Agent must announce when finishing or handing off a task
- Blockers and delays must copy `pm`
- User anxiety or high-pressure team situations should copy `ce`

## License
MIT License