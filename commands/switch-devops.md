---
description: Switch to DevOps/infrastructure mode — Terraform + AWS + Kubernetes context
---

# Switch to DevOps Mode

Load the **devops** context profile for Terraform + AWS + Kubernetes infrastructure work.

## Active Context
- **Rules**: `rules/terraform/`, `rules/kubernetes/`, `rules/aws/`, `rules/common/`
- **Agents**: terraform-reviewer, k8s-reviewer, aws-architect, infra-security-reviewer
- **Focus**: Security-first, blast radius awareness, cost optimization, state safety

## CRITICAL Guardrails
- **NEVER** `terraform apply` without reviewing plan output first
- **NEVER** `kubectl apply/delete` on production without confirmation
- **ALWAYS** run security scan before merging IaC changes
- **ALWAYS** check blast radius (count of affected resources)
- **FLAG** any IAM with wildcard Action/Resource, any public S3, any unencrypted storage

## Quick Commands Available
- `/tf-plan-review` — Terraform plan + security review
- `/k8s-audit` — Kubernetes manifest audit
- `/aws-cost-check` — Cost impact analysis
- `/infra-security-scan` — Full IaC security scan

## Diagnostic Quick-Reference
```bash
# Terraform
terraform fmt -check && terraform validate && tflint && checkov -d . --quiet

# Kubernetes
kubeval --strict -d manifests/ && kube-score score manifests/*.yaml

# AWS
infracost breakdown --path .
```
