# OpenClaw Autopilot Skill

一个让 Codex/OpenClaw 更适合长期自动工作的 skill。它的目标是减少“一句话动一下”的体验：先确认目标和完成标准，然后持续执行安全的下一步，定期整理上下文，遇到真正阻塞或高风险操作时再回来问用户。

`open-claw-autopilot` 适合 Windows 上的 OpenClaw/Codex 使用，尤其适合调试、安装配置、浏览器操作、文件修改、跑测试、修 bug、实现功能、长期排查问题等多步骤任务。

`open-claw-autopilot` is a Codex/OpenClaw skill for long-running autonomous work. It helps an agent move beyond "one user message, one action" by establishing a clear goal, continuously executing safe next steps, managing context, avoiding stalls, and reporting results with evidence.

This skill is especially useful when using OpenClaw or Codex on Windows for tasks such as debugging, project setup, UI navigation, browser work, file editing, test runs, and implementation work that should continue until completion instead of stopping after every small step.

## Quick Start

1. Copy the whole `open-claw-autopilot` folder into your skills directory.
2. Restart OpenClaw/Codex or reload skills.
3. Start a task with:

```text
使用 open-claw-autopilot，一直工作直到完成。开始前先确认目标、完成标准和不能碰的范围。
```

Windows skill path:

```text
%USERPROFILE%\.codex\skills\open-claw-autopilot\
```

macOS/Linux skill path:

```text
~/.codex/skills/open-claw-autopilot/
```

## Who This Is For

Use this skill if you want an AI agent to:

- Keep working across many steps instead of waiting after every action.
- Ask fewer routine questions while still respecting safety boundaries.
- Clarify what "done" means before starting.
- Keep a compact checkpoint for long tasks.
- Recover after context loss, restart, or handoff.
- Avoid repeating the same failed action over and over.
- Report final results with evidence, not just confidence.

## Core Idea

The skill gives the agent a practical operating contract:

- **Goal contract**: confirm the goal, completion criteria, verification method, scope boundaries, and stop conditions.
- **Autopilot loop**: act, observe, update the plan, check for stalls, keep going.
- **Context checkpoint**: maintain `OPENCLAW_CONTEXT.md` for long work.
- **Safety boundaries**: local reversible actions are okay; destructive, external, paid, credential, deployment, push, or publish actions require user confirmation.
- **Evidence discipline**: completion must be verified with tests, logs, screenshots, command output, UI state, or other observable proof.

## What It Does

The skill gives the agent a working protocol for:

- Confirming the task goal and completion criteria before starting.
- Checking available tools, operating system, shell, permissions, and constraints.
- Continuing autonomously through safe local steps.
- Asking the user only when a decision, permission, credential, or risky action is required.
- Creating compact context checkpoints for long tasks.
- Resuming from `OPENCLAW_CONTEXT.md` after context loss, restart, or handoff.
- Detecting repeated loops, stalls, and low-progress behavior.
- Retrying failures carefully without getting stuck.
- Verifying completion with evidence instead of assumptions.
- Delivering a clear final summary of changes, checks, limits, and next use.

## Why This Exists

Some OpenClaw/Codex workflows can feel like the agent only moves after each user message. That is frustrating for tasks that naturally require many steps: inspect files, run commands, debug errors, edit code, rerun tests, check UI behavior, and summarize the outcome.

This skill turns those tasks into a continuous loop:

1. Clarify the goal.
2. Determine what "done" means.
3. Act on the next safe step.
4. Observe the result.
5. Update the plan.
6. Compress context when needed.
7. Keep going until completion, a real blocker, or a safety boundary.

## Installation

Copy the entire `open-claw-autopilot` folder into your Codex/OpenClaw skills directory.

On Windows, the final layout should look like this:

```text
%USERPROFILE%\.codex\skills\open-claw-autopilot\SKILL.md
%USERPROFILE%\.codex\skills\open-claw-autopilot\agents\openai.yaml
```

On macOS or Linux, the layout is usually:

```text
~/.codex/skills/open-claw-autopilot/SKILL.md
~/.codex/skills/open-claw-autopilot/agents/openai.yaml
```

After copying the folder, restart OpenClaw/Codex or reload skills so the new skill is discovered.

## How To Use

Call the skill at the start of a task where you want autonomous progress:

```text
使用 open-claw-autopilot，帮我完成这个任务。你要一直自动推进，直到完成或真的遇到阻塞，不要每一步都等我确认。
```

Short version:

```text
用 open-claw-autopilot，一直做直到完成。
```

If your client supports `$SkillName` syntax:

```text
使用 $open-claw-autopilot，一直工作直到完成。
```

## Recommended Prompt Pattern

For best results, give the agent a clear goal contract:

```text
使用 open-claw-autopilot。

目标：修复登录按钮点击后没有反应的问题。
完成标准：
1. 点击登录按钮后能进入 dashboard。
2. 控制台没有相关错误。
3. 本地测试或手动验证通过。

限制：
- 不要自动提交或推送代码。
- 不要删除用户数据。
- 用中文汇报进度。
```

If you do not provide completion criteria, the skill tells the agent to ask:

```text
我先确认一下：这次任务的目标是什么，做到什么算完成，有没有不能碰的范围或必须保留的东西？
```

## Long-Running Context Management

For long tasks, the skill tells the agent to maintain a compact checkpoint named:

```text
OPENCLAW_CONTEXT.md
```

The checkpoint can include:

- Goal
- Completion criteria
- User preferences
- Current state
- Key decisions
- Evidence and verification results
- Changed files
- Blockers
- Next 1-3 actions

This prevents context from growing endlessly and makes recovery easier if the session restarts or the task is handed off to another agent.

## Safety Boundaries

The skill allows routine local actions such as reading files, editing task-relevant files, running tests, starting local dev servers, checking logs, and making reversible project-level changes.

It tells the agent to ask before actions such as:

- Deleting or overwriting many files.
- Running destructive commands.
- Installing system-wide software.
- Changing OS-level security settings.
- Committing, pushing, publishing, deploying, or sending external messages.
- Spending money or consuming paid resources beyond normal expected usage.
- Entering credentials, secrets, 2FA codes, personal data, or private keys.

## Designed For Windows/OpenClaw Work

The skill includes Windows-specific guidance:

- Identify whether the shell is PowerShell, CMD, Git Bash, or WSL.
- Avoid blindly applying macOS/Linux commands in a Windows shell.
- Verify paths, quoting, environment variables, and process handling.
- Distinguish Windows paths from WSL/Linux or remote paths.

## Included Files

```text
open-claw-autopilot/
├── SKILL.md
└── agents/
    └── openai.yaml
```

`SKILL.md` contains the full behavior protocol.

`agents/openai.yaml` contains UI-facing metadata for the skill.

## Status

The skill has been validated with the Codex skill validator:

```text
Skill is valid!
```

## License

No license has been selected yet. Add a license before publishing publicly if you want others to reuse, modify, or redistribute this skill.
