# Cloud Module

## Purpose

Provides cloud architecture guidance across AWS, GCP, and Azure. Selects appropriate managed services, designs cloud-native patterns, and prevents common cost and security mistakes.

---

## Responsibilities

- Recommend cloud services matched to workload requirements
- Design cloud-native architectures (serverless, containers, managed DB)
- Review cloud configurations for security, cost, and reliability
- Guide lift-and-shift and cloud-native migration strategies
- Advise on multi-region and high-availability patterns
- Estimate cost and identify cost optimisation opportunities

---

## Activation Criteria

Activate when:
- AWS, GCP, Azure, or "cloud" is mentioned
- Serverless, managed services, or cloud-native patterns are discussed
- Infrastructure cost is a concern
- Multi-region, disaster recovery, or availability zone design is needed
- Migration from on-premise to cloud is being planned

---

## Inputs

- Cloud provider (if stated or inferred from context)
- Workload characteristics (stateless/stateful, traffic pattern, data size)
- Scale requirements
- Budget constraints
- Data residency or compliance requirements
- Existing infrastructure

---

## Outputs

- Service selection with rationale
- Architecture description with trade-offs
- Security configuration recommendations
- Cost considerations
- Migration path (if applicable)

---

## Dependencies

- `modules/system-design.md` — distributed system patterns
- `modules/cybersecurity.md` — cloud security posture
- `modules/devops.md` — deployment and IaC
- `modules/networking.md` — VPC and network design

---

## Cloud-Native Design Principles

### 1. Prefer managed services for non-differentiating workloads
Running your own PostgreSQL cluster is a significant operational burden. Use RDS/Cloud SQL/Azure Database. Your differentiating work is your product, not database operations.

### 2. Design for failure
Assume any component can fail at any time. Design redundancy at every tier. Use multiple availability zones for stateful components.

### 3. Elasticity over over-provisioning
Cloud pricing is pay-per-use. Right-size and scale horizontally instead of provisioning peak capacity permanently.

### 4. Infrastructure as code, always
No manual console changes in production. All resources are reproducible from code.

### 5. Tag everything
Resource tags enable cost attribution, security auditing, and automation. Define a tagging standard before you start.

---

## Service Selection by Use Case

### Compute

| Workload | AWS | GCP | Azure |
|---|---|---|---|
| Long-running HTTP service | ECS Fargate or EKS | Cloud Run or GKE | Container Apps or AKS |
| Event-driven / short-lived | Lambda | Cloud Functions | Azure Functions |
| Batch / HPC | Batch | Batch | Azure Batch |
| VM-based (legacy lift) | EC2 | Compute Engine | Azure VMs |
| ML training | SageMaker | Vertex AI | Azure ML |

**Decision guide:**
- Stateless HTTP service with variable traffic → **Serverless containers** (Fargate / Cloud Run / Container Apps) — no cluster management, scales to zero
- Stateless HTTP service with consistent traffic → **Container service** — predictable cost
- Complex microservices with many teams → **Kubernetes** (EKS / GKE / AKS) — full control, significant operational overhead
- Simple event-driven functions → **FaaS** (Lambda / Cloud Functions) — lowest operational overhead

---

### Storage

| Use Case | AWS | GCP | Azure |
|---|---|---|---|
| Object storage | S3 | Cloud Storage | Blob Storage |
| Block storage (VM disks) | EBS | Persistent Disk | Azure Disk |
| File storage (shared) | EFS | Filestore | Azure Files |
| Archival (cold) | S3 Glacier | Coldline/Archive | Archive Storage |

**S3 / GCS / Blob Storage checklist:**
- [ ] Block public access by default
- [ ] Enable versioning for critical data
- [ ] Enable server-side encryption
- [ ] Configure lifecycle policies for cost management
- [ ] Enable access logging
- [ ] Restrict CORS to known origins

---

### Database

| Database type | AWS | GCP | Azure |
|---|---|---|---|
| PostgreSQL | RDS / Aurora PostgreSQL | Cloud SQL | Azure Database for PostgreSQL |
| MySQL | RDS / Aurora MySQL | Cloud SQL | Azure Database for MySQL |
| NoSQL document | DynamoDB | Firestore | Cosmos DB |
| NoSQL wide-column | Keyspaces | Bigtable | Cosmos DB (Cassandra API) |
| In-memory cache | ElastiCache (Redis/Memcached) | Memorystore | Azure Cache for Redis |
| Search | OpenSearch | — | Azure AI Search |
| Time series | Timestream | — | Azure Data Explorer |
| Data warehouse | Redshift | BigQuery | Synapse |

**Database selection heuristics:**
- ACID transactions required → PostgreSQL (RDS/Cloud SQL)
- Flexible schema, JSON documents → DynamoDB or Firestore (if key-value access pattern); PostgreSQL JSONB (if relational + JSON)
- High read throughput, low latency → Add Redis cache layer in front of any relational DB
- Analytical queries on large datasets → Redshift or BigQuery

---

### Messaging and Queues

| Use Case | AWS | GCP | Azure |
|---|---|---|---|
| Simple queue (point-to-point) | SQS | Cloud Tasks | Service Bus Queue |
| Pub/Sub (fan-out) | SNS | Pub/Sub | Service Bus Topic / Event Grid |
| Event streaming | Kinesis | Pub/Sub with retain | Event Hubs |
| Dead letter queues | SQS DLQ | Pub/Sub DLQ | Service Bus DLQ |

**When to use a queue:**
- Decouple producer from consumer
- Absorb traffic spikes
- Retry failed operations
- Async processing of long-running tasks

---

### Networking

| Component | AWS | GCP | Azure |
|---|---|---|---|
| Virtual network | VPC | VPC | VNet |
| Load balancer (L7) | ALB | Cloud Load Balancing | Application Gateway |
| Load balancer (L4) | NLB | TCP/UDP LB | Azure Load Balancer |
| CDN | CloudFront | Cloud CDN | Azure CDN / Front Door |
| DNS | Route 53 | Cloud DNS | Azure DNS |
| VPN | Site-to-site VPN | Cloud VPN | VPN Gateway |
| Private connectivity | PrivateLink | Private Service Connect | Private Link |

---

## Security Defaults

Apply these to every cloud deployment:

### IAM
- Principle of least privilege: every role has only the permissions it needs
- No wildcard permissions (`*`) in production roles
- Service accounts/roles for applications, not user credentials
- Enable MFA on all human accounts, especially root/owner
- Rotate access keys regularly; prefer short-lived credentials (IRSA, Workload Identity)

### Network
- All databases in private subnets (no public IP)
- Security groups: default deny inbound; explicit allow per service
- VPC Flow Logs enabled
- No SSH from 0.0.0.0/0; use SSM Session Manager (AWS) or IAP (GCP)

### Storage
- Encryption at rest: enabled by default on all managed services
- Encryption in transit: TLS enforced
- S3 Block Public Access: enabled at account level
- Enable versioning and MFA Delete for critical buckets

### Logging and Audit
- AWS CloudTrail / GCP Audit Logs / Azure Monitor: enabled in all regions
- Centralise logs in a separate, read-only logging account
- Set retention policy (90 days minimum; 1 year for regulated industries)
- Alert on: root account activity, IAM policy changes, security group changes

---

## Cost Optimisation

### Compute
- Right-size instances: use metrics to identify over-provisioned instances
- Reserved/committed use: 1–3 year commitment for stable baseline workloads (30–60% savings)
- Spot/preemptible instances: for fault-tolerant batch workloads (up to 90% savings)
- Auto-scaling: scale down during off-peak hours

### Storage
- Lifecycle policies: automatically move old objects to cheaper tiers
  ```
  0–30 days:    Standard (hot)
  30–90 days:   Infrequent Access
  90–365 days:  Glacier/Coldline
  365+ days:    Deep Archive
  ```
- Delete unused snapshots and volumes
- Compress before storing where applicable

### Data Transfer
- Keep data transfer within a region and AZ where possible
- Egress to internet is expensive; use CDN to serve content
- VPC endpoints (AWS) / Private Google Access (GCP) avoid internet egress for service calls

### Monitoring cost
- Set billing alerts before you are surprised
- Use cloud provider cost anomaly detection
- Review unused resources monthly (stopped VMs, unattached volumes, idle load balancers)

---

## High Availability Design

### Multi-AZ (within region)

Minimum for production:
- Compute: spread across 2+ AZs behind a load balancer
- Database: multi-AZ with automated failover (RDS Multi-AZ, Cloud SQL HA)
- Cache: Redis cluster mode or multi-AZ replication group

```
Region: us-east-1

AZ-a: [API servers] [DB primary]
AZ-b: [API servers] [DB standby ← automatic failover]
              ↑
       [Load balancer — spans both AZs]
```

### Multi-region

Only when you have strict availability or data residency requirements. Complexity is significant.

**Patterns:**
- Active-passive: traffic to primary region; failover to secondary
- Active-active: traffic split across regions; requires globally distributed data store

**Data replication considerations:**
- Strong consistency across regions is expensive (cross-region latency)
- Use eventual consistency for most data; strong consistency only where required
- Define RPO (Recovery Point Objective) and RTO (Recovery Time Objective) before designing

---

## Examples

### Example 1 — Three-tier web app on AWS

**Requirements:** Node.js API, PostgreSQL, Redis cache, 10k–50k daily active users, single region

```
Internet
   │
   ▼
CloudFront (CDN)
   │
   ▼
Application Load Balancer (ALB)
   │
   ├── ECS Fargate (API) — 2 tasks, auto-scaling 2–10
   │         │
   │         ├── ElastiCache Redis (cache) — single-AZ for dev, Multi-AZ for prod
   │         └── RDS PostgreSQL (db) — Multi-AZ, db.t3.medium
   │
   └── S3 + CloudFront (static assets)

Infrastructure:
- VPC: public subnet (ALB), private subnet (Fargate, RDS, Redis)
- Secrets Manager: DB credentials, API keys
- CloudWatch: logs, metrics, alarms
- ECR: container image registry
```

**Estimated monthly cost (us-east-1):**
- Fargate (2 tasks, 0.25 vCPU, 0.5GB): ~$15/task = $30
- RDS PostgreSQL db.t3.medium Multi-AZ: ~$100
- ElastiCache cache.t3.micro: ~$15
- ALB: ~$20
- S3 + CloudFront: ~$5–20
- **Total: ~$170–$185/month** at baseline

---

## Best Practices

- Use managed services — your operational burden is their business
- Design for multi-AZ from the start; retrofitting is hard
- Tag all resources before they number in the hundreds
- Set billing alerts on day one
- Treat infrastructure changes like code changes: PR review, test in staging

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Database in public subnet | Direct internet exposure |
| Root account access keys | Catastrophic if leaked |
| No billing alerts | Surprise invoice |
| All resources in one AZ | Single point of failure |
| Manual resource creation | Configuration drift; unreproducible |
| Over-provisioned instances | Wasted cost |

---

## Limitations

- Cloud service capabilities change frequently — verify current offerings
- Pricing changes; always validate with the provider's pricing calculator
- Multi-cloud adds significant complexity; require strong justification before pursuing
- Compliance specifics (HIPAA, PCI-DSS, SOC 2) require specialist review
