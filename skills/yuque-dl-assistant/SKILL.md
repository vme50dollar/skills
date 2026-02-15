---
name: yuque-dl-assistant
description: Download Yuque knowledge-base content to local Markdown with the yuque-dl CLI. Use when users ask to export or sync Yuque docs, run incremental downloads, include attachments, output TOC, or troubleshoot token/session and enterprise cookie-key issues. Use this skill for CLI workflow only (no GUI).
---

# Yuque DL Assistant

## Overview

Use this skill to complete Yuque export tasks with `yuque-dl` from request clarification to command execution and troubleshooting.
Keep the interaction practical: ask only for inputs that materially affect command correctness.

## Scope

- Use CLI flow only.
- Do not propose or run GUI workflows.
- Prefer direct command execution after required inputs are confirmed.

## Workflow

### 1) Confirm task inputs

Collect these inputs before running commands:

- Knowledge base URL (required)
- Output directory (default: `download`)
- Whether incremental mode is needed (`--incremental`)
- Whether images should be skipped (`--ignoreImg`)
- Whether attachments should be skipped (`--ignoreAttachments` with optional extensions)
- Whether TOC should be generated (`--toc`)
- Whether footer should be hidden (`--hideFooter`)

Use concise confirmation prompts such as:

- `请给我要下载的语雀知识库 URL。`
- `是否要增量下载（--incremental）？`
- `附件要全部下载，还是忽略全部/忽略指定后缀？`

### 2) Ensure CLI is available

Check CLI first:

```bash
yuque-dl --version
```

If command is missing:

- Ask: `检测到未安装 yuque-dl，是否允许我现在执行 npm i -g yuque-dl 自动安装？`
- If user agrees, run:

```bash
npm i -g yuque-dl
```

- Verify again with `yuque-dl --version`.
- If user declines, provide manual install command and pause execution.

### 3) Decide whether session/token is required

Use this decision logic:

- Public docs with no restricted resources: token may be optional.
- Private/shared/enterprise/password-protected or attachment-heavy cases: token is usually required.
- Enterprise domain (not `yuque.com`) may require custom cookie key via `-k`.

If token/key is missing, stop and ask targeted questions instead of blind retries.

### 4) Handle missing session/token interactively

When token is missing, respond with:

- Why token is needed for this case.
- How to get token and cookie key (load `references/session-token-guide.md`).
- What exact values user should return (`-k` and `-t`).

Example prompt:

- `当前链接需要登录态。请在浏览器 DevTools -> Application -> Cookies 中找到 cookie；通常 key 是 _yuque_session（企业版可能不同）。把 key 和 value 发我后我继续执行。`

### 5) Build command deterministically

Base command:

```bash
yuque-dl "<url>"
```

Add options only when requested/needed:

- `-d "<dir>"`
- `-k "<cookie_key>"`
- `-t "<token>"`
- `--incremental`
- `--ignoreImg`
- `--ignoreAttachments`
- `--ignoreAttachments mp4,pdf`
- `--toc`
- `--hideFooter`

Token safety and parsing rule:

- If token starts with `-` or includes shell-sensitive chars, use `-t="<token>"` form.

### 6) Execute and troubleshoot

Run the assembled command and interpret failures:

- Unknown option around token: switch to `-t="..."`.
- Attachment download failures: request login token and retry.
- Network interruptions: suggest rerun because progress is resumable.
- Enterprise cookie mismatch: ask user to provide actual cookie key from browser.

## Response style inside this skill

- Keep responses short and action-oriented.
- Ask one focused question at a time when information is missing.
- Do not ask questions that do not affect command correctness.
- Always include the exact command before execution when user confirmation is needed.

## Reference loading

Load `references/session-token-guide.md` when token/session uncertainty appears, especially for:

- Private libraries
- Password-protected public shares
- Enterprise Yuque domains
- Attachment permission errors
