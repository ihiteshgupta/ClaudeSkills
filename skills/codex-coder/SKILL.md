---
name: codex-coder
description: Use this skill for any coding task — writing features, test cases, or automation scripts. This skill MUST use the Codex MCP (mcp__codex-mcp__codex_task) to execute all coding work. Never write code manually — always delegate to Codex MCP.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, mcp__codex-mcp__codex_task, mcp__codex-mcp__codex_review, mcp__codex-mcp__codex_apply, mcp__codex-mcp__codex_discard, mcp__codex-mcp__codex_list
---

# Codex Coder Skill

## Core Rule
**ALL coding tasks MUST be executed via the Codex MCP — never write code manually.**
Always call `mcp__codex-mcp__codex_task` to delegate the work to Codex.

## Trigger
Use this skill when the user asks to:
- Write or implement any code (features, functions, modules)
- Generate test cases (unit, integration, e2e)
- Create automation scripts (CI/CD, data pipelines, cron jobs, shell scripts)
- Refactor or fix existing code

## Execution Steps

### 1. Identify the project directory
- Use the current working directory or ask the user if unclear
- Must be an absolute path to a git repository

### 2. Call Codex MCP to do the coding
Call `mcp__codex-mcp__codex_task` immediately with:
- `project_dir`: absolute path to the git project
- `prompt`: detailed task description (language, framework, file paths, expected behavior)

### 3. Review the output
Call `mcp__codex-mcp__codex_review` with the returned task ID to show the diff to the user.

### 4. Apply or Discard
- If user approves → call `mcp__codex-mcp__codex_apply`
- If user rejects → call `mcp__codex-mcp__codex_discard` and re-prompt with corrections

### Check in-progress tasks
Call `mcp__codex-mcp__codex_list` to list all pending/active Codex tasks.

## Prompt Guidelines for Codex
Write detailed prompts for best results:

**Feature / Code:**
```
Using [language/framework], implement [feature] in [file path].
Input: [describe], Output: [describe].
Follow existing code style and conventions.
```

**Test Cases:**
```
Write [Jest/pytest/Mocha] tests for [file/function].
Cover: happy path, edge cases, and error scenarios.
Mock external dependencies. Follow existing test structure.
```

**Automation Script:**
```
Create an automation script in [language] that [does X].
Trigger: [manual/cron/event]. Input: [source]. Output: [result].
```

## Rules
- NEVER write code yourself — always use `mcp__codex-mcp__codex_task`
- ALWAYS call `codex_review` before `codex_apply`
- If the diff is wrong, call `codex_discard` and re-run with a better prompt
- Break large tasks into focused, single-concern prompts for best results
