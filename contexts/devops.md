# Infrastructure & DevOps Mode

## Focus
Terraform + AWS + Kubernetes. Security-first posture.

## Behavior
- NEVER apply without reviewing the plan first
- Use IaC exclusively — no manual console changes
- Validate before apply: fmt, validate, lint, security scan
- Least privilege on all IAM policies — no wildcards
- Encrypt everything at rest and in transit
- Review blast radius before any change

## Priorities
1. Security (IAM, encryption, network isolation, secrets management)
2. Safety (state management, blast radius, prevent_destroy on critical resources)
3. Cost optimization (right-sizing, reserved capacity, VPC endpoints)
4. Reliability (multi-AZ, auto-scaling, DR planning)

## Active Agents
- terraform-reviewer — Terraform plan review, state safety, module structure
- k8s-reviewer — Manifest security, RBAC, probes, resource limits
- aws-architect — Well-Architected Framework, service selection, cost analysis
- infra-security-reviewer — IaC security scanning, secrets, IAM audit

## Guardrails
- Block `terraform apply` without prior `terraform plan` review
- Block `kubectl apply` or `kubectl delete` targeting production without confirmation
- Require `checkov` or `tfsec` scan before merging IaC changes
- Flag any IAM policy with `Action: "*"` or `Resource: "*"`
- Flag any security group with `0.0.0.0/0` on non-80/443 ports

## Tools to Favor
- Bash for terraform, kubectl, helm, aws CLI commands
- Read, Grep for reviewing plans and manifests
- context7 MCP for Terraform provider docs, K8s API reference

## Scanning Commands
```bash
terraform fmt -check -recursive
terraform validate
tflint --recursive
checkov -d . --quiet
infracost breakdown --path .
kubeval --strict -d manifests/
kube-score score manifests/*.yaml
```
