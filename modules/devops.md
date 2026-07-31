# DevOps Module

## Purpose

Designs and reviews CI/CD pipelines, deployment strategies, infrastructure automation, observability stacks, and operational practices. Bridges development and operations concerns.

---

## Responsibilities

- Design CI/CD pipelines tailored to team and risk level
- Recommend deployment strategies with risk trade-offs
- Review and generate infrastructure-as-code (IaC)
- Design observability stacks (logs, metrics, traces, alerts)
- Advise on containerisation and orchestration
- Define incident response and on-call practices

---

## Activation Criteria

Activate when:
- CI/CD pipeline design or review is requested
- Deployment strategy is being decided
- Docker, Kubernetes, or container orchestration is discussed
- Infrastructure-as-code (Terraform, Pulumi, CloudFormation) is involved
- Monitoring, alerting, or observability is discussed
- Runbooks or incident response processes are needed

---

## Inputs

- Application type and tech stack
- Deployment target (cloud provider, Kubernetes, serverless, VM)
- Team size and operational maturity
- Reliability requirements (SLA/SLO targets)
- Current practices and tooling
- Release frequency goals

---

## Outputs

- Pipeline design with stages and gates
- Deployment strategy recommendation
- IaC patterns and examples
- Observability setup
- Runbook templates

---

## Dependencies

- `modules/cloud.md` — cloud service integration
- `modules/testing.md` — test stage design
- `modules/networking.md` — network configuration
- `modules/cybersecurity.md` — security scanning in pipeline

---

## CI/CD Pipeline

### Standard pipeline stages

```
[Commit pushed]
      │
      ▼
[01] Source checkout + dependency install
      │
      ▼
[02] Lint + static analysis
      │
      ▼
[03] Unit tests          ← fast; fail fast
      │
      ▼
[04] Build artifact (binary / image)
      │
      ▼
[05] Integration tests   ← against real dependencies in Docker
      │
      ▼
[06] Security scan       (SAST, dependency vulnerability scan)
      │
      ▼
[07] Push artifact to registry
      │
      ▼
[08] Deploy to staging
      │
      ▼
[09] Smoke tests / E2E on staging
      │
      ▼
[10] Manual approval gate (for production)    ← optional, risk-dependent
      │
      ▼
[11] Deploy to production
      │
      ▼
[12] Health check + rollback if failed
```

### Pipeline design principles

- **Fail fast:** Put the fastest checks first (lint, unit tests). Do not run a 10-minute integration suite before a 30-second lint check.
- **Every commit is deployable:** The pipeline should produce a deployable artefact on every successful run, even if not every commit is deployed.
- **Immutable artefacts:** Build once; promote the same artefact through environments. Do not rebuild for staging vs production.
- **Automated rollback:** If a health check fails post-deploy, trigger rollback automatically.

### Pipeline as code

All pipeline configuration lives in source control alongside the code it builds.

```yaml
# Example: GitHub Actions structure
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Lint
        run: ruff check .
      - name: Unit tests
        run: pytest tests/unit -v --tb=short
      - name: Integration tests
        run: docker compose up -d && pytest tests/integration -v
```

---

## Deployment Strategies

### Recreate

```
[Old version] → STOP → [New version] → START
```

- Downtime: Yes
- Risk: High (no rollback during deploy)
- Use: Dev/test environments only

---

### Rolling

```
Instance 1: [Old] → [New]
Instance 2:        [Old] → [New]
Instance 3:               [Old] → [New]
```

- Downtime: No
- Risk: Medium (old and new run simultaneously for a window)
- Requirement: API must be backward compatible during rollout
- Use: Standard production deploys

---

### Blue / Green

```
[Blue: v1 — live]    ←── traffic
[Green: v2 — ready]

Switch:
[Blue: v1]
[Green: v2 — live]   ←── traffic

Rollback: switch back to Blue
```

- Downtime: No
- Risk: Low (instant rollback by switching traffic)
- Cost: 2x infrastructure during deployment
- Use: High-availability systems, large releases

---

### Canary

```
[v1: 95% traffic]
[v2: 5% traffic]  ← monitor for errors/latency

If healthy → gradual increase → 100% v2
If degraded → route 0% to v2 → investigate
```

- Downtime: No
- Risk: Very low (limited blast radius)
- Complexity: Requires traffic splitting and monitoring
- Use: High-risk changes, user-facing changes

---

### Feature flags

Decouple deployment from release. Code is deployed but feature is off.

```python
if feature_flags.is_enabled("new_checkout", user_id):
    return new_checkout_flow(cart)
else:
    return legacy_checkout_flow(cart)
```

- Downtime: No
- Risk: Very low (can disable without deploy)
- Overhead: Flag management complexity
- Use: A/B testing, phased rollouts, kill switches

---

## Containerisation

### Dockerfile best practices

```dockerfile
# Use explicit, pinned base image
FROM python:3.12.3-slim AS base

WORKDIR /app

# Copy dependency files first (layer cache)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source last
COPY src/ ./src/

# Run as non-root user
RUN useradd -m appuser
USER appuser

# Explicit port documentation
EXPOSE 8080

# Use exec form (not shell form) for PID 1
CMD ["python", "-m", "src.main"]
```

### Multi-stage builds

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build

# Production stage — only the build output
FROM node:20-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER node
CMD ["node", "dist/main.js"]
```

Reduces final image size significantly. Does not include build tools, source code, or dev dependencies.

---

## Infrastructure as Code

### Terraform patterns

```hcl
# modules/database/main.tf — encapsulate related resources
variable "environment" { type = string }
variable "instance_class" { type = string default = "db.t3.medium" }

resource "aws_db_instance" "main" {
  identifier        = "${var.environment}-postgres"
  engine            = "postgres"
  engine_version    = "15.4"
  instance_class    = var.instance_class
  allocated_storage = 20
  storage_encrypted = true
  deletion_protection = var.environment == "production"

  # ... additional configuration
}
```

### IaC principles
- **Everything in code:** No manual cloud console changes in production
- **Modules:** Reuse patterns via modules; do not copy-paste resources
- **Remote state:** State stored in S3/GCS, not locally
- **State locking:** Prevent concurrent applies
- **Plan before apply:** Always review `terraform plan` output
- **Separate state per environment:** Dev and prod are separate state files

---

## Observability

### The three pillars

**Logs** — what happened

```json
{
  "timestamp": "2025-07-30T14:23:01.123Z",
  "level": "error",
  "message": "Payment processing failed",
  "request_id": "req_01J3K...",
  "user_id": "usr_01H9...",
  "error": "CardDeclined",
  "duration_ms": 230
}
```

Key practices:
- Structured JSON logs (not free-form strings)
- Correlation IDs on every request (propagate through services)
- Never log credentials, tokens, or PII

---

**Metrics** — how much / how fast

Use the RED method per service:
- **R**ate — requests per second
- **E**rrors — error rate
- **D**uration — latency percentiles (p50, p95, p99)

And the USE method for infrastructure:
- **U**tilisation — what fraction of capacity is used
- **S**aturation — how much work is queued
- **E**rrors — error rate

```python
# Example: Prometheus metrics in Python
from prometheus_client import Counter, Histogram

request_count = Counter('http_requests_total', 'Total requests', ['method', 'endpoint', 'status'])
request_latency = Histogram('http_request_duration_seconds', 'Latency', ['endpoint'])
```

---

**Traces** — why it was slow

Distributed tracing connects a request across multiple services. Every operation is a span with a parent; together they form a trace.

```
[browser]
  └── [API gateway: 5ms]
        └── [auth service: 12ms]
        └── [order service: 245ms]
              └── [database query: 230ms]  ← bottleneck
              └── [cache lookup: 2ms]
```

Tools: Jaeger, Zipkin, AWS X-Ray, Datadog APM, OpenTelemetry (standard).

---

### Alerting

**Alert on symptoms, not causes.**

```
❌ Alert: CPU > 80%          (cause — not user-facing)
✓  Alert: p99 latency > 2s  (symptom — user impact)

❌ Alert: Disk > 70% full    (cause — not yet user-facing)
✓  Alert: Disk < 10GB free  (symptom — imminent impact)
```

**Alert properties:**
- Every alert must have a runbook: what to do when this fires
- Alerts must be actionable: if you can't act on it, it shouldn't page
- Use severity levels: P1 (wake someone up), P2 (fix in business hours), P3 (track as tech debt)
- Review alert noise monthly — silence or fix alerts that fire without action

---

## SLOs and Error Budgets

### Service Level Objective (SLO)

A target reliability level, expressed as a percentage over a time window.

```
SLO: 99.9% of requests return 2xx in < 500ms over 30 days
Error budget: 0.1% × 30 days × 24h × 60min = 43.2 minutes of downtime/errors per month
```

### Error budget policy

- If error budget is healthy: ship faster, take more risk
- If error budget is at 50%: slow down, focus on reliability
- If error budget is exhausted: freeze releases until budget recovers

---

## Runbook Template

```markdown
# Runbook: [Alert Name]

**Severity:** P1 / P2 / P3
**Owner:** [Team]
**Last reviewed:** [Date]

## What is this alert?
[What condition triggered it. What service is affected.]

## Impact
[What users experience when this fires.]

## Immediate actions
1. [First thing to check]
2. [Second thing to check]
3. [Escalation path if not resolved in N minutes]

## Diagnostic commands
```bash
# Check service health
kubectl get pods -n [namespace]
# Check recent logs
kubectl logs -n [namespace] deploy/[service] --since=5m
# Check metrics
[link to dashboard]
```

## Common causes and fixes
| Symptom | Cause | Fix |
|---|---|---|
| [Symptom] | [Cause] | [Fix] |

## Escalation
[Who to contact and how if this cannot be resolved]
```

---

## Examples

### Example 1 — GitHub Actions pipeline with Docker

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: testpassword
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: '3.12' }
      - run: pip install -r requirements.txt
      - run: ruff check .
      - run: pytest tests/ -v --tb=short
        env:
          DATABASE_URL: postgresql://postgres:testpassword@localhost/testdb

  build-and-push:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

---

## Best Practices

- Every deployment is automated — no manual steps in production
- Every deployment is reversible — define rollback before deploying
- Ship small, ship often — large deployments are riskier than frequent small ones
- Define SLOs before building alerting — alerts serve the SLO
- Runbooks are mandatory for every P1/P2 alert

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Manual production deployments | Inconsistency, human error, undocumented changes |
| No staging environment | First test in production |
| Alerting on causes not symptoms | Alert fatigue; missing real user impact |
| No rollback plan | Outage extended by inability to revert |
| Building Docker image per environment | Configuration drift; not immutable |

---

## Limitations

- Kubernetes depth: patterns covered are introductory; production K8s requires specialist knowledge
- Cloud-specific services (ECS, Cloud Run, App Service) detailed in `modules/cloud.md`
- Security scanning tooling selection depends on language and platform
