# Coding Agent Cheat Sheet

> Snapshot: July 2026. Agent tools change quickly. In Claude Code, type `/` or run `/help` to see what your installation currently supports. Some review commands may come from skills or plugins rather than the core installation.

## Claude Code: Context Management

| Command | Purpose | Core habit |
| --- | --- | --- |
| `/context` | Shows how much of the context window is in use. | Yes |
| `/compact` | Summarizes the conversation to free context while preserving the thread. | Yes |
| `/add-dir` | Adds another directory to the session so the agent can read outside the current repository. | Yes |
| `/clear` | Starts a fresh conversation with empty context; the previous one remains saved. | Useful |
| `/cost` or `/usage` | Shows token usage and session cost information. | Useful |
| `/btw` | Asks a side question without adding it to the main conversation context. | Optional |
| `/resume` | Returns to a previous conversation. | Optional |

**Rule of thumb:** use `/context` as the fuel gauge. Compact when useful history is buried under completed exploration; clear when the task changes.

## Claude Code: Review and Quality

| Command | Purpose | Availability |
| --- | --- | --- |
| `/code-review` | Reviews a diff for bugs and improvements; may accept an effort level and apply fixes. | Check `/help` |
| `/security-review` | Looks for injection, XSS, authentication, and unsafe data-handling problems. | Check `/help` |
| `/review` | Performs a quick, single-pass review of a GitHub pull request. | Check `/help` |
| `/verify` | Checks end to end that the implementation behaves as required. | Skill-dependent |
| `/simplify` | Improves reuse, efficiency, and readability without acting as a bug hunt. | Skill-dependent |
| `/diff` | Opens the interactive diff viewer. | Version-dependent |

For an adversarial review, use an agent or session that did not write the original change. Its goal should be to find evidence that the change is wrong, not confirm it.

## Claude Code: Session, Model, and Harness

| Command | Purpose | Core habit |
| --- | --- | --- |
| `/init` | Analyzes the repository and creates an initial `CLAUDE.md`. | Yes |
| `/memory` | Edits `CLAUDE.md` files and manages automatic memory. | Useful |
| `/model` | Changes the active model. | Yes |
| `/effort` | Adjusts reasoning effort from low to maximum; more effort usually uses more tokens. | Yes |
| `/mcp` | Connects, reconnects, enables, or disables MCP servers. | Yes |
| `/plan` | Enters read-only planning: the agent investigates and proposes before editing. | Yes |
| `/agents` | Manages configured subagents. | Useful |
| `/rewind` | Restores code and conversation to an earlier checkpoint. | Useful |
| `/doctor` | Diagnoses and repairs the Claude Code installation. | Troubleshooting |

A slash command can also be a custom skill. When a workflow repeats, package its instructions as a reusable `/name` command instead of rewriting them each time.

---

## Agent Vocabulary

| Term | Meaning |
| --- | --- |
| **Gate** | A stopping point that requires a check or human decision before work continues. |
| **Harness** | The system around the model: tools, memory, instructions, permissions, and controls. |
| **Agentic loop** | The repeating cycle: gather context, act, verify, and continue. |
| **Tool call / tool use** | The model reads, edits, searches, or executes something instead of only producing text. |
| **Context window** | The session's token-limited working memory. |
| **Context rot** | Reduced reliability as the context grows and useful information competes with noise. |
| **Thinking / effort** | How much reasoning the model performs before answering. |
| **Hallucination** | A false statement presented as if it were true. |
| **Grounding** | Connecting an answer to real files, command output, documentation, or other evidence. |
| **Scaffold** | The foundation an agent creates before implementing the final feature. |

## Terms Seen in Claude Code

| Term | Meaning |
| --- | --- |
| **Permission mode** | Controls how often Claude asks before acting. Cycle modes with `Shift+Tab`. |
| **Plan mode** | Read and propose only; changes require human approval. |
| **Auto-accept edits** | File edits proceed without confirmation; review afterward with `git diff`. |
| **Auto mode** | Actions proceed automatically, but a classifier blocks dangerous operations. It is not the same as auto-accept. |
| **Bypass permissions** | Disables permission checks, commonly called "YOLO" mode. The real option is `--dangerously-skip-permissions`; use only in an isolated environment. |
| **Hook** | A deterministic command triggered by an event such as `PreToolUse` or `Stop`; it can block an action. |
| **Subagent** | A specialized agent with its own context window that returns a focused result. |
| **Slash command / skill** | A reusable `/name` instruction set, loaded when needed. |
| **Checkpoint / rewind** | A local snapshot created around prompts; `/rewind` restores it. This does not replace Git. |
| **MCP** | The standard used to connect external tools such as Jira, GitHub, and databases. |
| **Background agent** | An agent working independently without occupying the current terminal conversation. |
| **Worktree** | An isolated Git checkout that lets agents work in parallel without overwriting one another. |

> **Important:** auto-accept edits removes edit confirmations. Auto mode still applies a safety classifier. Permission bypass removes the safety gate entirely.

---

## The Same Action Across Tools

Commands are not one-to-one. The capability may live in a command, editor control, product feature, or configuration file.

| Action | Claude Code | Cursor | GitHub Copilot |
| --- | --- | --- | --- |
| View context usage | `/context` | No direct equivalent | Context meter in VS Code chat input |
| Compact context | `/compact` | `/summarize` in CLI; automatic near the limit | `/compact` |
| Start fresh | `/clear` | New Chat button or `/new-chat` | `/clear`; `/fork` branches a conversation |
| Add focused context | `/add-dir` and targeted reads | `@Files`, `@Docs`, `@Terminals`, `@Commit`, `@Branch`, `@Browser` | `#codebase`, `#file`, `#selection`, `@terminal`, `@github`, `@vscode` |
| Review code | `/code-review`, `/security-review` | Bugbot or Agent Review | Copilot Code Review on the PR; `/fix` in chat |
| Project memory | `CLAUDE.md`; `/init` | `.cursor/rules/*.md`, `AGENTS.md` | `.github/copilot-instructions.md`, `AGENTS.md` |
| Connect MCP | `/mcp`; `claude mcp add` | `.cursor/mcp.json`; `/mcp` | `.vscode/mcp.json`; `copilot mcp add`; `/mcp` |
| Choose model | `/model` | Model selector or `/model` in CLI | Model picker or `/model` in CLI |

## Cursor Quick Reference

| Action | Cursor surface |
| --- | --- |
| Compact or summarize | `/summarize` in CLI; automatic summarization near the limit |
| Start a new conversation | New Chat in the editor or `/new-chat` in CLI |
| Select context | Editor `@` mentions such as `@Files` and `@Docs` |
| Review | Bugbot on a PR or Agent Review with `/agent-review` |
| Change Agent/Ask/Plan/Manual mode | Editor mode selector or `Shift+Tab` |
| Store project rules | `.cursor/rules/*.md`, `AGENTS.md`, or `/create-rule` |
| Configure MCP | `.cursor/mcp.json` or `/mcp` |
| Define custom commands | `.cursor/commands/*.md`; invoke with `/` |

Cursor has no direct `/context` token command in this snapshot. Context selection is mainly controlled through editor mentions and its CLI.

## GitHub Copilot Quick Reference

| Action | Copilot surface |
| --- | --- |
| View context usage | Meter in the VS Code chat input |
| Compact | `/compact` in VS Code; automatic when full |
| Start or branch a conversation | `/clear` or `/fork` |
| Add context | `#codebase`, `#file`, `#selection`, `@vscode`, `@terminal`, `@github` |
| Review code | Copilot Code Review on a PR or `/fix` in chat |
| Generate tests or docs | `/tests`, `/doc` |
| Choose model | VS Code model picker or `/model` in CLI |
| Store project instructions | `.github/copilot-instructions.md` and `AGENTS.md` |
| Create reusable prompts | `*.prompt.md`, exposed as `/name` |
| Configure MCP | `.vscode/mcp.json`, `copilot mcp add`, or `/mcp` |
| Manage CLI sessions | `/session`, `/usage`, `/add-dir` |

---

## What to Memorize

Do not memorize every command. Remember the actions:

1. **Inspect context** before the session becomes crowded.
2. **Compact or restart** when old work no longer helps.
3. **Plan before editing** when the change is broad or risky.
4. **Review with an independent perspective** before committing.
5. **Ground conclusions** in files, tests, and command output.
6. **Use the safest permission mode** that still supports the task.
7. **Turn repeated workflows into skills or reusable prompts.**
8. **Check `/help` and official documentation** because command surfaces change frequently.
