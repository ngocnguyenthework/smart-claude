# SmartClaude Workflow Guide

Your adapted Claude Code configuration for backend (NestJS + FastAPI), DevOps (Terraform + AWS + K8s), and frontend (React + Next.js + Tailwind + shadcn/ui) development.

---

## Table of Contents

1. [Installation](#installation)
2. [Directory Structure](#directory-structure)
3. [Context Switching](#context-switching)
4. [Daily Workflow Patterns](#daily-workflow-patterns)
5. [Commands Reference](#commands-reference)
6. [Agent Reference](#agent-reference)
7. [Security Posture for IaC](#security-posture-for-iac)
8. [Shortform vs Longform Strategy](#shortform-vs-longform-strategy)
9. [Tips](#tips)

---

## Installation

### Option A: Symlink into your project (recommended)

Link the relevant pieces into your project's `.claude/` directory:

```bash
# From your project root
mkdir -p .claude/{agents,rules,commands}

# Agents — copy the ones you need
cp /path/to/smartclaude/agents/*.md .claude/agents/

# Rules — copy stack-specific rules
cp -r /path/to/smartclaude/rules/common .claude/rules/
cp -r /path/to/smartclaude/rules/nestjs .claude/rules/     # if NestJS project
cp -r /path/to/smartclaude/rules/fastapi .claude/rules/    # if FastAPI project
cp -r /path/to/smartclaude/rules/terraform .claude/rules/  # if IaC project
cp -r /path/to/smartclaude/rules/kubernetes .claude/rules/ # if K8s project
cp -r /path/to/smartclaude/rules/aws .claude/rules/        # if AWS project
cp -r /path/to/smartclaude/rules/frontend .claude/rules/   # if frontend project

# Commands — copy to user-level for global access
cp /path/to/smartclaude/commands/*.md ~/.claude/commands/
```

### Option B: User-level installation (all projects)

```bash
# Agents (user-level)
cp /path/to/smartclaude/agents/*.md ~/.claude/agents/

# Rules (user-level — applies to all projects)
cp -r /path/to/smartclaude/rules/* ~/.claude/rules/

# Commands (user-level)
cp /path/to/smartclaude/commands/*.md ~/.claude/commands/

# Contexts (for dynamic system prompt injection)
cp -r /path/to/smartclaude/contexts ~/.claude/contexts/
```

### Option C: Dynamic system prompt injection

Use context profiles as system prompts per session:

```bash
# Backend session
claude --system-prompt "$(cat /path/to/smartclaude/contexts/backend.md)"

# DevOps session
claude --system-prompt "$(cat /path/to/smartclaude/contexts/devops.md)"

# Frontend session
claude --system-prompt "$(cat /path/to/smartclaude/contexts/frontend.md)"

# Shell aliases (add to ~/.zshrc or ~/.bashrc)
alias claude-backend='claude --system-prompt "$(cat ~/.claude/contexts/backend.md)"'
alias claude-devops='claude --system-prompt "$(cat ~/.claude/contexts/devops.md)"'
alias claude-frontend='claude --system-prompt "$(cat ~/.claude/contexts/frontend.md)"'
```

### MCP Servers

Merge the servers from `mcp-configs/mcp-servers.json` into your `~/.claude.json`:

```bash
# View the config
cat /path/to/smartclaude/mcp-configs/mcp-servers.json

# Manually merge into ~/.claude.json under "mcpServers"
# Replace YOUR_GITHUB_PAT_HERE and YOUR_FIRECRAWL_KEY_HERE with actual values
```

### Hooks

Merge hooks from `hooks/hooks.json` into your `~/.claude/settings.json`:

```bash
# View the hooks
cat /path/to/smartclaude/hooks/hooks.json

# Merge the hooks arrays into your existing settings.json
```

---

## Directory Structure

```
smartclaude/
  WORKFLOW_GUIDE.md            ← You are here
  agents/                      # 8 stack-specialized agents
    nestjs-reviewer.md         # NestJS code review
    fastapi-reviewer.md        # FastAPI code review
    terraform-reviewer.md      # Terraform plan review
    k8s-reviewer.md            # K8s manifest review
    aws-architect.md           # AWS architecture design
    database-reviewer.md       # PostgreSQL + ORM review
    infra-security-reviewer.md # IaC security scanning
    frontend-reviewer.md       # React/Next.js/Tailwind review
  rules/
    common/                    # 10 universal rules
    nestjs/                    # NestJS: coding-style, security, testing, patterns
    fastapi/                   # FastAPI: coding-style, security, testing, patterns
    terraform/                 # Terraform: coding-style, security, testing, patterns
    kubernetes/                # K8s: coding-style, security, testing, patterns
    aws/                       # AWS: security, patterns
    frontend/                  # React/Next.js/Tailwind/shadcn: coding-style, nextjs,
                               #   tailwind, shadcn-ui, security, testing, patterns
  commands/                    # 10 workflow commands
    nestjs-scaffold.md         # /nestjs-scaffold
    fastapi-scaffold.md        # /fastapi-scaffold
    tf-plan-review.md          # /tf-plan-review
    k8s-audit.md               # /k8s-audit
    db-migrate.md              # /db-migrate
    aws-cost-check.md          # /aws-cost-check
    infra-security-scan.md     # /infra-security-scan
    switch-backend.md          # /switch-backend
    switch-devops.md           # /switch-devops
    switch-frontend.md         # /switch-frontend
  contexts/                    # 3 system prompt profiles
    backend.md                 # NestJS + FastAPI + PostgreSQL
    devops.md                  # Terraform + AWS + K8s
    frontend.md                # React + Next.js + Tailwind + shadcn/ui
  hooks/
    hooks.json                 # PreToolUse + PostToolUse + Stop hooks
  mcp-configs/
    mcp-servers.json           # 5 curated MCP servers
```

---

## Context Switching

### Method 1: Slash Commands (within a session)

```
/switch-backend    → Loads backend rules, agents, and priorities
/switch-devops     → Loads DevOps rules, agents, and security guardrails
/switch-frontend   → Loads frontend rules, agents, and UI focus
```

These commands tell Claude which rules and agents to prioritize for the rest of the session.

### Method 2: Shell Aliases (per session)

```bash
# Add to ~/.zshrc
alias claude-be='claude --system-prompt "$(cat ~/.claude/contexts/backend.md)"'
alias claude-ops='claude --system-prompt "$(cat ~/.claude/contexts/devops.md)"'
alias claude-fe='claude --system-prompt "$(cat ~/.claude/contexts/frontend.md)"'
```

Then start dedicated sessions: `claude-be`, `claude-ops`, or `claude-fe`.

### Method 3: Project-Level Rules

Copy only the relevant rules into your project's `.claude/rules/`:
- NestJS project → copy `rules/common/` + `rules/nestjs/`
- FastAPI project → copy `rules/common/` + `rules/fastapi/`
- Terraform project → copy `rules/common/` + `rules/terraform/` + `rules/aws/` + `rules/kubernetes/`
- Next.js project → copy `rules/common/` + `rules/frontend/`
- Full-stack → copy all

---

## Daily Workflow Patterns

### Backend Feature (NestJS)

```
1. /switch-backend
2. /plan                      → Plan the feature implementation
3. /nestjs-scaffold users     → Generate module structure
4. Write business logic in service
5. /tdd                       → Test-driven development cycle
6. /db-migrate                → Generate migration if needed
7. /code-review               → Review before commit
8. Commit and push
```

### Backend Feature (FastAPI)

```
1. /switch-backend
2. /plan                      → Plan the feature
3. /fastapi-scaffold orders   → Generate domain structure
4. Write business logic in service
5. pytest --cov               → Run tests with coverage
6. /db-migrate                → Alembic migration if needed
7. /code-review               → Review before commit
8. Commit and push
```

### Infrastructure Change (Terraform)

```
1. /switch-devops
2. /plan                      → Plan the infrastructure change
3. Write Terraform code
4. /tf-plan-review            → Review plan output (MANDATORY)
5. /infra-security-scan       → Security scan (MANDATORY)
6. /aws-cost-check            → Cost impact analysis
7. Commit, PR, merge
8. terraform apply            → Only after PR approval
```

### Kubernetes Deployment

```
1. /switch-devops
2. Update manifests or Helm values
3. /k8s-audit                 → Audit for security and best practices
4. kubectl diff -f manifests/ → Preview changes
5. Commit, PR, merge
6. ArgoCD sync or kubectl apply
```

### Frontend Feature (Next.js)

```
1. /switch-frontend
2. /plan                      → Plan the feature/component
3. Build Server Components for data fetching
4. Build Client Components for interactivity
5. Use shadcn/ui components (extend, don't edit)
6. npx vitest run             → Run component tests
7. /code-review               → Review before commit
8. npm run build              → Verify SSR works
9. Commit and push
```

### Quick Bug Fix (any stack)

```
1. /switch-<relevant-mode>
2. Read the bug report
3. Reproduce → Find root cause
4. Write failing test
5. Fix the bug
6. Verify test passes
7. /code-review
8. Commit with "fix: <description>"
```

---

## Commands Reference

| Command | Mode | Purpose |
|---------|------|---------|
| `/switch-backend` | Any | Enter NestJS + FastAPI + PG mode |
| `/switch-devops` | Any | Enter Terraform + AWS + K8s mode |
| `/switch-frontend` | Any | Enter React + Next.js + Tailwind mode |
| `/nestjs-scaffold` | Backend | Scaffold NestJS module |
| `/fastapi-scaffold` | Backend | Scaffold FastAPI domain |
| `/db-migrate` | Backend | ORM-aware database migration |
| `/tf-plan-review` | DevOps | Terraform plan + security review |
| `/k8s-audit` | DevOps | K8s manifest audit |
| `/aws-cost-check` | DevOps | AWS cost impact analysis |
| `/infra-security-scan` | DevOps | Full IaC security scan |

---

## Agent Reference

| Agent | Model | Invoked When |
|-------|-------|-------------|
| `nestjs-reviewer` | Sonnet | NestJS code changes |
| `fastapi-reviewer` | Sonnet | FastAPI code changes |
| `terraform-reviewer` | Sonnet | Terraform file changes |
| `k8s-reviewer` | Sonnet | K8s manifest changes |
| `aws-architect` | **Opus** | Architecture decisions, service selection |
| `database-reviewer` | Sonnet | Schema, migration, query changes |
| `infra-security-reviewer` | Sonnet | Before any infrastructure deployment |
| `frontend-reviewer` | Sonnet | React/Next.js/Tailwind changes |

**Model selection**: All agents use Sonnet (best coding model) by default. `aws-architect` uses Opus for deep reasoning on architectural trade-offs.

---

## Security Posture for IaC

These rules are enforced by hooks and agents:

### Terraform
- `terraform apply` is **blocked** without prior plan review (hook)
- IAM wildcards (`*`) are flagged as CRITICAL
- Secrets in `.tf`/`.tfvars` are flagged as CRITICAL
- `checkov` scan required before merge

### Kubernetes
- `kubectl apply` to production is **blocked** without confirmation (hook)
- Containers running as root are flagged as CRITICAL
- `cluster-admin` RBAC is flagged as CRITICAL
- Missing resource limits are flagged as CRITICAL

### AWS
- Public S3 buckets flagged as CRITICAL
- Unencrypted storage flagged as HIGH
- Missing VPC Flow Logs flagged as MEDIUM
- Cost increases > 10% flagged as WARNING

### General
- `--no-verify` on git operations is **blocked** (hook)
- `console.log` in JS/TS files triggers a warning (hook)
- Python files auto-formatted with `ruff` on save (hook)
- TS/JS files auto-formatted with `prettier` on save (hook)

---

## Shortform vs Longform Strategy

Based on the ECC guides, here's when to use each approach:

### Shortform (Quick, Focused Tasks)
- Scaffolding a NestJS module or FastAPI domain
- Single-file bug fixes
- Adding a new API endpoint
- Writing a Helm values override
- Small Terraform changes (single resource)
- Adding a shadcn/ui component to a page

### Longform (Deep, Multi-Step Tasks)
- Designing AWS architecture (use `aws-architect` agent)
- Multi-service feature spanning backend + frontend + infra
- Database schema redesign with migration strategy
- Terraform module refactoring across environments
- Security audit across the full IaC codebase
- Performance optimization spanning queries + caching + CDN
- Incident response and root cause analysis

### Parallelization
- **Main terminal**: Code changes
- **Fork/second terminal**: Questions about codebase, research, docs
- **Git worktrees**: When multiple Claude instances edit overlapping files
- **Cascade method**: New tasks in tabs to the right, sweep left to right

---

## Tips

### Context Window Management
- Keep under 5 MCPs enabled per session (github + context7 always; others as needed)
- Use `/compact` when exploration context is no longer needed
- Use `/switch-*` commands to focus rules loading

### Session Handoff
When hitting context limits on a long task:
1. Ask Claude to summarize current state to a file
2. Start fresh session
3. Provide the summary file path to continue

### Shell Aliases

```bash
# Add to ~/.zshrc or ~/.bashrc
alias c='claude'
alias cbe='claude --system-prompt "$(cat ~/.claude/contexts/backend.md)"'
alias cops='claude --system-prompt "$(cat ~/.claude/contexts/devops.md)"'
alias cfe='claude --system-prompt "$(cat ~/.claude/contexts/frontend.md)"'
```

### Pre-Commit Checklist
```bash
# Backend (NestJS)
npx tsc --noEmit && npm run lint && npm test -- --coverage

# Backend (FastAPI)
ruff check . && ruff format --check . && mypy src/ && pytest --cov=src

# Infrastructure
terraform fmt -check && terraform validate && checkov -d . --quiet

# Frontend
npx tsc --noEmit && npx eslint . && npx vitest run && npm run build
```
