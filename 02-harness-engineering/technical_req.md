# Technical Requirements

Use this checklist before starting the course. Install only what is missing.

## 1. Check Your Computer

Run these commands in the terminal where you plan to work:

```bash
git --version
node -v
claude --version
gh --version
make --version
```

Each command should print a version number. If one says `command not found` or `is not recognized`, install that tool using the section below.

### Reference Environment

These versions were verified in the course author's WSL environment on October 6, 2026. They are a known working combination, not exact version requirements:

- Git: `2.53.0`
- Node.js: `v24.21.0`
- Claude Code: `2.1.291`
- GitHub CLI: `2.46.0`
- GNU Make: `4.4.1`

---

## 2. Git and GitHub Access

Git manages code versions, and GitHub hosts the course repository.

Install Git from [git-scm.com/downloads](https://git-scm.com/downloads). On Windows, Git for Windows also provides Git Bash.

Choose one GitHub authentication method:

### Option A: SSH

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
cat ~/.ssh/id_ed25519.pub
ssh -T git@github.com
```

Add the public key ending in `.pub` to **GitHub > Settings > SSH and GPG keys**. Never share the private key.

Follow the [GitHub SSH guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) if you need system-specific steps.

### Option B: HTTPS

Use repository addresses beginning with `https://github.com/` and let GitHub CLI store your credentials.

Both address formats point to the same repository:

```text
git@github.com:user/repository.git
https://github.com/user/repository.git
```

---

## 3. Node.js

Node.js runs the JavaScript and TypeScript project used in the course.

Install the current **LTS** release from [nodejs.org/en/download](https://nodejs.org/en/download). The project requires Node.js **20.19 or newer**.

Check it with:

```bash
node -v
```

If you already use an older Node.js version for another project, install a version manager instead of replacing it manually.

---

## 4. Claude Code

Claude Code is the terminal-based coding agent used in the course.

Follow the official [Claude Code quickstart](https://code.claude.com/docs/en/quickstart). Then run:

```bash
claude --version
claude
```

The first interactive launch opens browser authentication. Claude Code requires an eligible paid plan, so confirm access before the live session on the official [pricing page](https://claude.com/pricing).

You do not need an Anthropic API key for normal local use.

---

## 5. GitHub CLI (`gh`)

GitHub CLI lets you authenticate, inspect pull requests, and read diffs from the terminal.

Install it from [cli.github.com](https://cli.github.com), then run:

```bash
gh auth login
gh auth status
```

During login, choose the same SSH or HTTPS method selected for Git.

---

## 6. `make`

`make` runs project shortcuts such as setup, tests, and database preparation.

- **macOS:** run `xcode-select --install`.
- **Linux:** install `build-essential` on Debian/Ubuntu, or the `make` package for your distribution.
- **Windows:** the recommended course environment is WSL. Open PowerShell as administrator, run `wsl --install`, and follow the [Microsoft WSL guide](https://learn.microsoft.com/en-us/windows/wsl/install).

Windows and WSL are separate environments. If you work inside WSL, install Node.js, `gh`, and `make` inside WSL and verify them from its terminal.

---

## 7. Jira Setup

Create a free Jira Cloud account from the [Atlassian signup page](https://www.atlassian.com/try/cloud/signup?bundle=jira-software&edition=free). Choose the site address carefully because changing it may require a paid plan.

Create a Kanban or Scrum board with these values:

- **Name:** `FlowSync`
- **Key:** `FLOW`

The key is important because course prompts use it to identify the board. If Jira generates another key, change it before creating work items.

Also check the work-item type menu and note:

- The type used for a user story.
- Whether a subtask type is available.

Jira interfaces may use the newer terms **space** instead of **project** and **work item** instead of **issue**. APIs and JQL may still use the older names.

---

## 8. Connect Jira Through MCP

MCP allows the coding agent to read and update Jira without manually copying tickets into the chat.

Use Atlassian's official [Rovo MCP server instructions](https://www.atlassian.com/platform/remote-mcp-server). Jira Cloud Free accounts can use the server, subject to plan-based rate limits.

Some connection methods require an [Atlassian API token](https://id.atlassian.com/manage-profile/security/api-tokens). Treat this token like a password:

- Never commit it to Git.
- Never place it in a shared file.
- Revoke it if it is exposed.

---

## 9. Quick Glossary

- **Git:** Tracks changes and manages branches.
- **Node.js:** Runs JavaScript code.
- **npm:** Installs JavaScript packages and runs project scripts.
- **GitHub CLI (`gh`):** Manages GitHub repositories and pull requests from the terminal.
- **Make:** Automates tasks such as testing and building.
- **Claude Code:** An AI agent that helps write, explain, and modify code.
- **CLI:** a program used by typing commands in a terminal. Git, `gh`, `make`, and `claude` are CLIs.
- **Terminal:** the window where commands are entered.
- **SSH:** authentication using a public and private key pair.
- **Ubuntu / WSL:** Your Linux environment inside Windows. Start it with:

	```bash
	wsl -d Ubuntu
	```

- **MCP:** a standard that connects an agent to external systems and tools.
- **API token:** a revocable secret used by software instead of a password.

---

## Final Readiness Check

Run the commands again in your actual working terminal:

```bash
git --version
node -v
claude --version
gh --version
make --version
```

You are ready when:

- All five commands return a version.
- `gh auth status` confirms GitHub access.
- `node -v` is `v20.19.0` or newer.
- Claude Code opens successfully.
- Your Jira board is named `FlowSync` with the key `FLOW`.
- You know the Jira user-story and subtask type names.
- The Jira MCP connection is configured.

Resolve missing tools or authentication problems before the live session.
