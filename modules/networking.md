# Networking Module

## Purpose

Provides guidance on network architecture, protocols, DNS, TLS, load balancing, firewall design, and network troubleshooting. Bridges application concerns with network reality.

---

## Responsibilities

- Design network architectures and segmentation strategies
- Advise on protocol selection and configuration
- Explain DNS, TLS, HTTP, and TCP/IP behaviour precisely
- Review network security configurations
- Diagnose networking issues from symptoms
- Advise on load balancing and traffic routing

---

## Activation Criteria

Activate when:
- Network design or topology is discussed
- DNS, TLS, HTTP, TCP, or protocol questions arise
- Load balancing, proxies, or traffic routing are involved
- Firewall rules, security groups, or network ACLs are configured
- "Connection refused", "timeout", or "SSL error" type problems
- VPN, peering, or private network connectivity is needed

---

## Inputs

- Network topology requirements
- Security requirements (what must be isolated)
- Traffic patterns (volumes, origins, protocols)
- Cloud or on-premise context
- Existing network layout (if modifying)

---

## Outputs

- Network architecture design
- Protocol and configuration recommendations
- Security group / firewall rule recommendations
- Troubleshooting diagnosis

---

## Dependencies

- `modules/cybersecurity.md` — network security posture
- `modules/cloud.md` — cloud-native networking
- `modules/system-design.md` — distributed system topology

---

## Network Design Principles

### Defence in depth
Layer security controls. Perimeter firewall + subnet isolation + host-based firewall + application-layer checks. No single failure should expose everything.

### Least privilege by default
Default deny. Explicitly allow only required traffic. Review and minimise open ports regularly.

### Segment by trust level
```
[Internet]
    │
[Public subnet]       ← Load balancers, CDN, bastion hosts
    │
[Application subnet]  ← API servers, web servers (no direct internet)
    │
[Data subnet]         ← Databases, caches, queues (application subnet only)
```

No database should be reachable from the internet. No application server should directly expose ports to the internet (use a load balancer).

### Encrypt in transit
TLS everywhere — including internal service-to-service traffic. Terminate TLS at the load balancer for external traffic; use mutual TLS (mTLS) for sensitive internal service communication.

---

## Protocol Reference

### HTTP / HTTPS

| Feature | HTTP | HTTPS |
|---|---|---|
| Transport | Plaintext | TLS encrypted |
| Default port | 80 | 443 |
| Use in production | Redirect to HTTPS only | Yes |

**HTTP status code classes:**
- `1xx` — Informational
- `2xx` — Success (200 OK, 201 Created, 204 No Content)
- `3xx` — Redirection (301 Moved Permanently, 302 Found, 304 Not Modified)
- `4xx` — Client error (400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 429 Too Many Requests)
- `5xx` — Server error (500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout)

**Common HTTP headers:**

| Header | Purpose |
|---|---|
| `Content-Type` | Media type of the body |
| `Authorization` | Credentials |
| `Cache-Control` | Caching directives |
| `X-Request-ID` | Correlation ID for tracing |
| `Strict-Transport-Security` | HSTS — force HTTPS |
| `Content-Security-Policy` | XSS mitigation |
| `X-Frame-Options` | Clickjacking mitigation |
| `X-Content-Type-Options: nosniff` | MIME type sniffing mitigation |

---

### TLS

**Minimum:** TLS 1.2. **Preferred:** TLS 1.3.

**Certificate management:**
- Use Let's Encrypt (via Certbot or ACME client) for public-facing services
- Use ACM (AWS), Managed Certificates (GCP), or App Service Certificates (Azure) for cloud
- Automate renewal — manual certificate management causes outages
- Set `HSTS` header to prevent HTTPS downgrade attacks

**Common TLS errors and causes:**

| Error | Likely Cause |
|---|---|
| `ERR_CERT_EXPIRED` | Certificate not renewed |
| `ERR_CERT_COMMON_NAME_INVALID` | Wrong domain on certificate |
| `UNABLE_TO_VERIFY_LEAF_SIGNATURE` | Missing intermediate certificate |
| `SSL_ERROR_RX_RECORD_TOO_LONG` | TLS on a port serving plain HTTP |
| `CERT_HAS_EXPIRED` | Server-side certificate validation failure |

---

### DNS

**Record types:**

| Record | Purpose | Example |
|---|---|---|
| A | IPv4 address | `api.example.com → 203.0.113.1` |
| AAAA | IPv6 address | `api.example.com → 2001:db8::1` |
| CNAME | Alias to another name | `www → example.com` |
| MX | Mail server | `example.com → mail.example.com` |
| TXT | Arbitrary text (SPF, DKIM, verification) | `"v=spf1 include:..."` |
| NS | Name server delegation | `example.com NS ns1.registrar.com` |
| SOA | Zone authority | (managed by DNS provider) |

**TTL (Time to Live):**
- Low TTL (60–300s): useful during migrations — changes propagate quickly
- High TTL (3600s+): reduces DNS lookup overhead; changes propagate slowly
- **Reduce TTL before migrations.** Increase it after.

**DNS propagation delay:**
Changes propagate within the TTL window. There is no instant global propagation — plan accordingly.

---

### TCP

**Three-way handshake:**
```
Client ──SYN──────────────► Server
Client ◄─────────SYN-ACK── Server
Client ──ACK──────────────► Server
[Connection established]
```

**Connection states:**
- `ESTABLISHED` — active connection
- `TIME_WAIT` — connection closed; waiting for delayed packets (2×MSL, usually 60–120s)
- `CLOSE_WAIT` — remote closed; local hasn't responded
- Large `TIME_WAIT` count: normal under high connection churn; use `SO_REUSEADDR`
- Large `CLOSE_WAIT` count: usually a bug — application not closing connections

---

## Load Balancing

### Algorithms

| Algorithm | Behaviour | Best For |
|---|---|---|
| Round Robin | Distributes equally | Stateless, uniform requests |
| Least Connections | Routes to least busy | Variable-length requests |
| IP Hash | Same IP → same backend | Session affinity (avoid if possible) |
| Weighted | Routes more to higher-capacity backends | Mixed backend sizes |
| Random with two choices | Near-optimal with low overhead | High-scale stateless |

### Layer 4 vs Layer 7

| | L4 (TCP) | L7 (HTTP) |
|---|---|---|
| Visibility | IP and port only | HTTP headers, paths, content |
| Routing basis | IP/port | URL path, header, cookie |
| TLS termination | Pass-through or terminate | Terminate |
| Performance | Lower overhead | Higher overhead, more features |
| Use for | Non-HTTP, raw TCP | HTTP APIs, routing by path/host |

### Health checks
Every load balancer must have health check configuration:
- Check the application, not just TCP connectivity
- Endpoint: `GET /health` → 200 OK (within 1 second)
- Unhealthy threshold: 2–3 consecutive failures before removing from rotation
- Healthy threshold: 2 consecutive successes before re-adding

---

## Firewall / Security Group Design

### Default rule set

```
Inbound:
  Allow  443  from 0.0.0.0/0    (HTTPS — public)
  Allow   80  from 0.0.0.0/0    (HTTP → redirect to HTTPS)
  Allow   22  from [VPN CIDR]   (SSH — restricted source)
  Deny  ALL   from 0.0.0.0/0    (default deny)

Outbound:
  Allow  443  to 0.0.0.0/0      (HTTPS calls to external APIs)
  Allow   53  to [DNS resolvers] (DNS)
  Allow [DB port] to [data subnet CIDR]
  Deny  ALL   to 0.0.0.0/0
```

### Common security group mistakes

- Allowing `0.0.0.0/0` inbound on database ports (SSH or DB port to internet)
- Allowing `0.0.0.0/0` outbound (use egress filtering)
- Overly broad CIDR ranges ("the /16 is fine")
- Unused rules never reviewed or removed

---

## Network Troubleshooting

### Diagnostic sequence

```
1. Can you reach the IP?        ping [ip]
2. Is the port open?            telnet [ip] [port] OR nc -zv [ip] [port]
3. Is DNS resolving?            nslookup [hostname] OR dig [hostname]
4. What is the route?           traceroute [ip]
5. What is the TLS cert?        openssl s_client -connect [host]:443
6. What are the headers?        curl -Iv https://[host][path]
7. Is it a firewall?            Check security groups / ACLs / iptables
```

### Common errors mapped to causes

| Error | Likely cause |
|---|---|
| `Connection refused` | Port not open; service not running; firewall blocks locally |
| `Connection timed out` | Firewall drops packet silently; wrong IP; routing issue |
| `No route to host` | Network unreachable; wrong subnet; routing table missing |
| `Name or service not known` | DNS not resolving; wrong hostname; DNS misconfiguration |
| `502 Bad Gateway` | Load balancer can't reach backend; backend crashed |
| `504 Gateway Timeout` | Backend too slow; upstream timeout too short |

---

## Examples

### Example 1 — VPC design for a three-tier web app

```
VPC: 10.0.0.0/16

Public subnets (one per AZ):
  10.0.1.0/24 (AZ-a) — Load balancer, NAT gateway, bastion
  10.0.2.0/24 (AZ-b)

Application subnets (one per AZ):
  10.0.11.0/24 (AZ-a) — API servers (no public IP)
  10.0.12.0/24 (AZ-b)

Data subnets (one per AZ):
  10.0.21.0/24 (AZ-a) — PostgreSQL primary, Redis
  10.0.22.0/24 (AZ-b) — PostgreSQL replica

Security groups:
  sg-lb:    inbound 443 from 0.0.0.0/0
  sg-app:   inbound 8080 from sg-lb only
  sg-data:  inbound 5432 from sg-app only
            inbound 6379 from sg-app only
```

---

## Best Practices

- No database reachable from the internet, ever
- SSH access via VPN or bastion host, never directly from internet
- Automate certificate renewal — manual renewal causes outages
- Reduce DNS TTL before any migration involving DNS changes
- Log all network traffic at boundary points (VPC flow logs, load balancer access logs)

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Database in public subnet | Direct internet exposure |
| SSH open to 0.0.0.0/0 | Brute force and exploitation target |
| Self-signed certificates in production | Client errors; disables warnings users learn to ignore |
| Missing health checks | Load balancer routes to dead backends |
| No egress filtering | Data exfiltration; unrestricted outbound |

---

## Limitations

- Physical network design (on-premise hardware) is outside scope
- Cloud-specific service configurations are in `modules/cloud.md`
- Advanced BGP, MPLS, and carrier-grade networking require specialist expertise
