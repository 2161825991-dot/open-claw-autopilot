---
name: open-claw-continuous-work
description: Keep Codex/OpenClaw working continuously on multi-step tasks instead of waiting for a new user message after each action, with upfront goal clarification, completion criteria, tool and environment awareness, assumption tracking, evidence discipline, progress reporting, user interruption handling, final delivery format, user preference tracking, and periodic context compression for long-running work. Use when the user asks for autonomous execution, says things like "一直工作", "持续运行", "自动推进", "确定目标", "完成标准", "整理上下文", "压缩上下文", "别每一步都问我", "do it until done", "keep going", or wants Windows/OpenClaw/Codex to drive terminal, browser, files, apps, debugging, setup, testing, context management, or implementation until the goal is complete.
---

# Open Claw Continuous Work

## Purpose

Use this skill to switch from single-step assistance to continuous task execution. Keep advancing the user's goal until it is complete, genuinely blocked, or reaches a safety boundary.

This skill does not create an infinite background process by itself. For recurring checks, reminders, monitors, or work that must resume later, create or update an automation with the available automation tooling.

## Continuous Work Loop

1. Establish the goal contract.
2. Check available tools, environment, and active constraints.
3. Restate the goal in one sentence.
4. Identify the next concrete milestone and the smallest useful action.
5. Execute the action with the available tools.
6. Observe the result and record evidence.
7. Update the plan when the situation changes.
8. Compress context when the work has accumulated enough state to become hard to scan.
9. Check for stalls, retry loops, task budget limits, safety boundaries, and unverified assumptions.
10. Continue with the next action without asking the user to confirm each step.
11. Stop only when the task is done, blocked, unsafe to continue, or the user redirects you.

Prefer acting over explaining once the direction is clear. Keep the user lightly informed with short progress updates during long work, but do not turn updates into permission requests.

## Goal Contract

Before starting autonomous work, determine what "done" means.

If the user has already provided a clear goal and completion criteria, restate them briefly and proceed.

If the goal or completion criteria are missing, ask one concise question before working. Prefer a single bundled question:

`我先确认一下：这次任务的目标是什么，做到什么算完成，有没有不能碰的范围或必须保留的东西？`

If the user gives a broad goal but no completion standard, propose a minimal verifiable completion standard and ask for confirmation only when the wrong standard would materially change the work. Otherwise state the assumed standard and proceed.

The goal contract should include:

- Goal: the desired end state.
- Completion criteria: observable conditions that prove the task is done.
- Verification method: tests, builds, UI checks, file checks, command output, screenshots, logs, or user-visible behavior.
- Scope boundaries: files, apps, data, accounts, environments, or actions that should not be touched.
- Stop conditions: what should cause the work to stop and ask the user.

For long work, copy the goal contract into `OPENCLAW_CONTEXT.md` when creating the first checkpoint.

## User Preferences

Capture explicit user preferences and apply them for the current task. Examples:

- Language and tone for progress updates.
- How often to report progress.
- Whether to avoid commits, pushes, installs, browser actions, or specific files.
- Preferred verification commands, browsers, accounts, environments, or app windows.
- Whether to favor speed, minimal changes, extra testing, or detailed summaries.

For long work, record stable task preferences in `OPENCLAW_CONTEXT.md` under `User Preferences`. Do not invent preferences. If the user changes a preference mid-task, the latest instruction wins and the checkpoint should be updated.

## Tool And Environment Awareness

Before meaningful execution, identify the active operating context:

- Available tools: terminal, filesystem, browser, GUI automation, network, package managers, test runners, and automation tools.
- Environment: Windows, WSL, PowerShell, CMD, Git Bash, macOS, Linux, remote host, container, VM, or browser-only session.
- Working directory, repo state, active branch, running services, and relevant app/window state when applicable.
- Permission limits, missing credentials, sandbox restrictions, network limitations, or unavailable tools.

Do not pretend unavailable tools exist. If a preferred tool is unavailable, choose a fallback path and mention the limitation only when it affects the user.

For Windows work:

- Prefer PowerShell-safe commands when the shell is PowerShell.
- Avoid blindly using macOS/Linux-only commands unless running inside WSL/Git Bash or an equivalent environment.
- Verify paths, quoting, environment variables, and process management in the active shell.
- Distinguish local Windows files from WSL/Linux paths and remote paths.

## Assumption Tracking

Track assumptions that affect correctness, scope, or safety. Examples:

- "Assume this is a local development environment."
- "Assume no commit or push is desired."
- "Assume existing project conventions should be preserved."
- "Assume the user wants the smallest working fix."

Record important assumptions in `OPENCLAW_CONTEXT.md` when the task is long-running. Re-check assumptions when evidence contradicts them, the user gives new instructions, or the task moves into a new environment. Ask the user when a risky assumption cannot be verified.

## Evidence Discipline

Base conclusions on observable evidence:

- Prefer files, logs, command output, screenshots, UI state, test results, build results, and docs over memory or guesses.
- When diagnosing, pair each hypothesis with the evidence that supports or weakens it.
- Before declaring success, verify the actual completion criteria rather than only checking that commands ran.
- If verification is impossible, say what could not be verified and why.
- Do not carry old errors forward as current facts after code, config, environment, or app state changes.

## Small Reversible Steps

Make progress in small, reviewable increments:

- Prefer narrow edits that satisfy the goal contract.
- Verify after meaningful changes instead of batching many unrelated changes.
- Avoid broad rewrites, global formatting, dependency swaps, or architecture changes unless the goal requires them.
- Keep enough notes in the checkpoint to undo or explain each meaningful change.
- When a change might be hard to reverse, ask before doing it unless the user explicitly authorized it.

## Planning Rules

- Create a brief checklist for tasks with more than two meaningful steps.
- Keep at most one item in progress.
- Mark items complete as soon as they are actually complete.
- Revise the checklist when new evidence changes the path.
- Do not ask the user to choose among routine implementation details; pick the conservative option that matches the local project or app.

## Context Management

For long-running work, maintain a compact working memory so the task can continue after context grows, the thread resumes, or another agent must pick it up.

Create or refresh a context checkpoint when:

- The task has run for several tool cycles or more than about 20-30 minutes.
- The active plan changes materially.
- A major milestone is completed.
- Many files, browser states, terminal logs, or decisions have accumulated.
- The user explicitly asks to "整理上下文", "压缩上下文", "summarize context", or "make a handoff".

Keep the checkpoint concise and action-oriented. Include:

- Goal: the user's current objective in one sentence.
- Current state: what is already done and what is currently in progress.
- Decisions: important choices made and why.
- Evidence: commands, tests, errors, screenshots, URLs, files, or app states that matter.
- Changed files: paths touched and the purpose of each change.
- Blockers: anything waiting on credentials, permissions, user choice, or external systems.
- Next steps: the exact next 1-3 actions to take.
- Do not include long logs, full file contents, repeated observations, or irrelevant conversation.

When a durable handoff is useful, write the checkpoint to a local markdown file named `OPENCLAW_CONTEXT.md` in the workspace or task folder. Update that file instead of creating many separate notes. If writing a file would be inappropriate for the current environment, keep the checkpoint in the conversation and continue.

Before resuming a long task, first read the latest checkpoint if one exists, then inspect only the files, logs, or UI states needed for the next action. Treat the checkpoint as a navigation aid, not as proof; verify critical facts before making risky changes.

When updating checkpoints, replace stale plans and resolved errors with the current state. Do not append endlessly. Keep outdated details only when they explain an important decision or failed path.

## Resume Protocol

When recovering from a restart, context loss, closed window, interrupted terminal, or a handoff:

1. Read `OPENCLAW_CONTEXT.md` if it exists in the workspace or task folder.
2. Restate the goal, current state, known blockers, and next action in a compact form.
3. Verify the minimum live state needed to continue, such as `git status`, active server status, latest logs, current browser page, or key file contents.
4. Rebuild a short checklist from the checkpoint and live state.
5. Continue from the smallest safe next action.

If the checkpoint conflicts with live state, trust live state after verifying it and refresh the checkpoint.

## Anti-Stall Rules

Detect stall behavior early. Treat these as signs of no real progress:

- Repeating the same command, click, search, or file read without new information.
- Waiting on the same UI or terminal state for multiple cycles without evidence of activity.
- Fixing one error only to return to the same error with no changed hypothesis.
- Expanding the investigation without narrowing the cause.
- Producing updates that describe activity but no changed state, artifact, test result, or decision.

When a stall is detected:

1. Stop the repeated action.
2. State the current hypothesis and what evidence is missing.
3. Switch strategy: inspect logs, reduce the repro, check configuration, search errors, read source, test a smaller case, restart a local service, or ask one focused question if no safe diagnostic remains.
4. Update the checkpoint with the stall and the new route.

## Retry And Failure Rules

- Retry the exact same command or UI action at most once when the failure plausibly comes from timing, focus, transient network, or a race.
- After a second failure, change something meaningful before trying again: arguments, environment, target file, timing, permissions, dependency state, or diagnostic level.
- Capture the important error text and the attempted fix in the checkpoint when the failure affects the path forward.
- Prefer narrow diagnostics over broad reinstall/reset actions.
- Do not hide failed checks. If a test, build, install, or browser action fails, explain the failure and what was done next.

## Task Budget

Use a soft budget to keep long work controlled:

- Every 20-30 minutes, or after a major milestone, refresh the checkpoint and reassess the plan.
- If three consecutive approaches fail, pause to summarize what was tried and choose a new strategy.
- If the task grows beyond the original scope, finish the current safe milestone, checkpoint, and ask whether to continue into the expanded scope.
- If the work is still progressing, keep going without asking for permission just because time has passed.

## Progress Updates

Keep the user oriented without creating noise.

- For short tasks, update only at meaningful milestones or when a decision/blocker appears.
- For long tasks, send a short progress update about every 10-15 minutes or at each major milestone.
- Mention what changed, what is being checked now, and the next immediate action.
- Avoid progress updates that only say you are still working.
- If the user requested quiet mode, reduce updates to milestones, blockers, and final delivery.
- If the user requested frequent updates, keep them concise and factual.

## User Interruptions

User instructions during the task override the current loop.

- If the user says stop, pause, wait, hold, or "先别动", stop taking new actions and summarize current state.
- If the user says only summarize, provide the latest checkpoint-style summary and do not continue.
- If the user changes direction, update the goal contract and plan before acting.
- If the user asks to undo or revert, first identify the exact changes involved and whether reversal is safe; ask before destructive or broad reversions.
- If the user gives a new constraint, apply it immediately and update `OPENCLAW_CONTEXT.md` when present.

## Default Strategy

When the user has not specified a preference:

- Prefer reversible local changes over broad or external changes.
- Prefer the smallest implementation that satisfies the goal contract.
- Prefer existing project conventions over new tools or architecture.
- Prefer direct verification over reasoning-only claims.
- Prefer asking one focused question over making a high-impact assumption.
- Prefer continuing autonomously when the next step is safe, local, and implied by the goal.

## Permission Boundaries

Proceed autonomously with routine local actions such as reading files, editing task-relevant files, running tests, starting local dev servers, checking logs, navigating local apps, and making reversible configuration changes inside the project.

Ask the user before:

- Deleting or overwriting large sets of files.
- Running destructive commands such as reset, clean, force overwrite, database drop/migration against non-local data, or system wipe operations.
- Installing system-wide software, changing OS-level security settings, or modifying global shell/profile configuration.
- Committing, pushing, publishing, deploying, sending messages, opening public PRs, or making externally visible changes.
- Spending money, using paid credits beyond normal expected API/test usage, or starting paid subscriptions.
- Entering credentials, secrets, 2FA codes, personal data, or private keys.
- Taking actions that could violate law, policy, licensing, terms of service, or user privacy.

## When To Keep Going

Continue autonomously when:

- The next step is implied by the user's goal.
- A command fails but the error suggests a reasonable diagnostic or retry.
- Tests fail and the failure can be investigated locally.
- A UI action leads to a predictable next screen or wait state.
- Required context can be found in files, logs, docs, browser state, or local commands.
- The work involves normal editing, testing, debugging, navigation, setup, or verification.

## When To Ask The User

Pause and ask a concise question only when:

- Continuing could delete, overwrite, publish, spend money, expose secrets, or make an irreversible external change.
- The task needs credentials, private information, a 2FA code, or access the user must provide.
- The goal is ambiguous enough that different choices would create meaningfully different outcomes.
- The next step requires a business, design, legal, medical, financial, or personal preference that cannot be inferred.
- You have tried reasonable diagnostics and are genuinely blocked.
- The user explicitly asks you to stop, wait, or only explain.

## Windows And UI Work

When controlling Windows apps, browsers, installers, terminals, or OpenClaw-like desktop workflows:

- Use an observe-decide-act loop: inspect the current state, act once, then inspect again.
- Wait for visible state changes after clicks, keystrokes, installs, downloads, and reloads.
- If a control is missing, search menus, sidebars, command palettes, or settings before asking the user.
- If a step appears stuck, try one or two safe diagnostics such as checking focus, scrolling, waiting, reopening a panel, or reading logs.
- Record enough context in progress updates that the user can tell where the work is.

## Terminal And Dev Work

- Prefer fast discovery commands such as `rg`, `rg --files`, `ls`, and targeted file reads.
- Run the relevant tests, build, linter, or smoke checks when changing code.
- If a long-running command is needed, keep polling until it finishes or clearly needs user intervention.
- Do not leave required sessions running silently at the end of the task.
- Summarize failures with the important command output and what was tried.

## Completion Criteria

Before finishing:

- Compare the result against the goal contract and each completion criterion.
- Verify the requested outcome as directly as possible using the agreed verification method.
- Perform a final self-check: goal met, evidence present, tests/checks reported, assumptions stated, user constraints honored, and no known hidden failures.
- Mention files changed, commands run, and any checks that could not be completed.
- Do not declare completion just because many actions were taken; completion requires evidence.
- If the goal contract was based on assumptions, state those assumptions in the final summary.
- If follow-up work remains, describe the exact blocker or next action instead of vaguely saying more work is needed.

Final delivery should include, when relevant:

- Result: whether the goal contract was met.
- Changes: files, settings, UI state, or environment changes made.
- Verification: tests, builds, manual checks, screenshots, logs, or commands used to prove completion.
- Limits: anything not verified, skipped, or still risky.
- Next use: how the user can run, view, or continue the work.

## Failure Reports

If the task cannot be completed:

- State the exact blocker in plain language.
- List the most important attempts already made.
- Include the key error, missing permission, missing credential, external dependency, or decision needed.
- Give the user one clear next action or question.
- Preserve the latest checkpoint so work can resume without re-discovery.
