# SmartClaude

A Claude Code configuration toolkit for full-stack and DevOps development. Pre-built rules, agents, commands, and hooks tuned for:

- **Backend** — NestJS, FastAPI, PostgreSQL
- **DevOps** — Terraform, AWS, Kubernetes
- **Frontend** — React, Next.js, Tailwind CSS, shadcn/ui

---

## What's Included

| Component | Count | Purpose |
|-----------|-------|---------|
| `agents/` | 8 | Stack-specialized code reviewers and architects |
| `rules/` | 7 stacks | Coding style, security, testing, and patterns per stack |
| `commands/` | 10 | Slash commands for scaffolding, auditing, and workflow |
| `contexts/` | 3 | System prompt profiles (backend / devops / frontend) |
| `hooks/` | — | PreToolUse/PostToolUse/Stop hooks with safety guardrails |
| `mcp-configs/` | — | Curated MCP server configuration |

---

## Quick Install

### Per-project (recommended)

```bash
# From your project root
mkdir -p .claude/{agents,rules,commands}

cp /path/to/smartclaude/agents/*.md .claude/agents/
cp -r /path/to/smartclaude/rules/common .claude/rules/
cp -r /path/to/smartclaude/rules/nestjs .claude/rules/   # pick your stack(s)
cp /path/to/smartclaude/commands/*.md ~/.claude/commands/
```

### User-level (all projects)

```bash
cp /path/to/smartclaude/agents/*.md ~/.claude/agents/
cp -r /path/to/smartclaude/rules/* ~/.claude/rules/
cp /path/to/smartclaude/commands/*.md ~/.claude/commands/
cp -r /path/to/smartclaude/contexts ~/.claude/contexts/
```

### Shell aliases for context-focused sessions

```bash
# Add to ~/.zshrc
alias claude-be='claude --system-prompt "$(cat ~/.claude/contexts/backend.md)"'
alias claude-ops='claude --system-prompt "$(cat ~/.claude/contexts/devops.md)"'
alias claude-fe='claude --system-prompt "$(cat ~/.claude/contexts/frontend.md)"'
```

See [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) for full installation options including MCP server and hooks setup.

---

## Context Switching

Switch focus mid-session with slash commands:

```
/switch-backend    → NestJS + FastAPI + PostgreSQL mode
/switch-devops     → Terraform + AWS + Kubernetes mode
/switch-frontend   → React + Next.js + Tailwind + shadcn/ui mode
```

---

## Key Agents

| Agent | Model | Use Case |
|-------|-------|----------|
| `nestjs-reviewer` | Sonnet | NestJS code review |
| `fastapi-reviewer` | Sonnet | FastAPI code review |
| `terraform-reviewer` | Sonnet | Terraform plan review |
| `k8s-reviewer` | Sonnet | Kubernetes manifest review |
| `aws-architect` | Opus | Architecture decisions |
| `database-reviewer` | Sonnet | Schema, migrations, queries |
| `infra-security-reviewer` | Sonnet | IaC security scanning |
| `frontend-reviewer` | Sonnet | React/Next.js/Tailwind review |

---

## Key Commands

| Command | Purpose |
|---------|---------|
| `/nestjs-scaffold <name>` | Scaffold NestJS module |
| `/fastapi-scaffold <name>` | Scaffold FastAPI domain |
| `/db-migrate` | ORM-aware database migration |
| `/tf-plan-review` | Terraform plan + security review |
| `/k8s-audit` | Kubernetes manifest audit |
| `/aws-cost-check` | AWS cost impact analysis |
| `/infra-security-scan` | Full IaC security scan |

---

## Safety Guardrails

Hooks enforce these rules automatically:

- `terraform apply` is blocked without a prior plan review
- `kubectl apply` to production requires confirmation
- `--no-verify` on git operations is blocked
- IAM wildcards, secrets in `.tf` files, and root containers are flagged as CRITICAL
- `console.log` in TS/JS triggers a warning
- Python files auto-format with `ruff`; TS/JS files auto-format with `prettier`

---

## Stack Rules Coverage

Each stack folder contains: `coding-style`, `security`, `testing`, `patterns`.

```
rules/
  common/       # git workflow, hooks, code review, performance, security
  nestjs/
  fastapi/
  terraform/
  kubernetes/
  aws/
  frontend/     # also includes nextjs, tailwind, shadcn-ui rules
```

---

## More

See [WORKFLOW_GUIDE.md](WORKFLOW_GUIDE.md) for daily workflow patterns, parallelization strategies, context window tips, and pre-commit checklists.
