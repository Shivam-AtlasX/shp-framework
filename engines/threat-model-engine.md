# Threat Model Engine

## Purpose

Evaluates the security posture of code, designs, and architectures. Applied when requests involve software, data handling, authentication, authorisation, or network communication.

---

## Threat Modelling Framework

SHP uses a structured STRIDE-informed approach, simplified for practical use:

| Category | Questions |
|---|---|
| **Spoofing** | Can an attacker impersonate a user, service, or component? |
| **Tampering** | Can an attacker modify data in transit or at rest? |
| **Repudiation** | Can actions be denied without evidence? Is audit logging present? |
| **Information Disclosure** | Can sensitive data be accessed by unauthorised parties? |
| **Denial of Service** | Can an attacker exhaust resources or block legitimate access? |
| **Elevation of Privilege** | Can an attacker gain more access than they should have? |

---

## Algorithm

```
function threatModel(artifact, context):

  # Identify components
  components = extractComponents(artifact)
  trust_boundaries = identifyTrustBoundaries(components)
  data_flows = identifyDataFlows(components)

  threats = []

  for data_flow in data_flows:
    if crosses(data_flow, trust_boundary):
      threats += evaluateSTRIDE(data_flow)

  for component in components:
    threats += evaluateComponent(component)

  # Filter to relevant, practical threats
  relevant_threats = filter(threats, severity >= MEDIUM)

  # Generate mitigations
  for threat in relevant_threats:
    mitigations = generateMitigations(threat)
    threat.mitigations = mitigations

  return ThreatReport(threats, mitigations)
```

---

## Trust Boundary Identification

Trust boundaries exist between:
- Client and server
- User input and system processing
- Service and external API
- Public and private network segments
- Authenticated and unauthenticated zones
- User role levels (read / write / admin)

Every data flow that crosses a trust boundary is a candidate threat surface.

---

## Severity Classification

| Severity | Criteria |
|---|---|
| Critical | Remote code execution, authentication bypass, mass data exposure |
| High | Privilege escalation, significant data exposure, injection vulnerabilities |
| Medium | Information leakage, missing rate limiting, weak cryptography |
| Low | Missing headers, verbose error messages, suboptimal configuration |

---

## Common Vulnerability Patterns

The engine checks for these patterns in generated code and designs:

**Injection**
- SQL injection (unparameterised queries)
- Command injection (unsanitised shell calls)
- XSS (unescaped output in HTML context)
- Path traversal (user-controlled file paths)

**Authentication and Session**
- Weak or missing token validation
- Session fixation
- Missing token expiry
- Insecure token storage (localStorage for sensitive tokens)
- Missing CSRF protection for state-changing operations

**Authorisation**
- Missing authorisation checks on sensitive endpoints
- IDOR (Insecure Direct Object References)
- Horizontal privilege escalation

**Cryptography**
- Hardcoded secrets or credentials
- Weak algorithms (MD5, SHA1 for passwords, ECB mode)
- Missing encryption for sensitive data at rest

**Configuration**
- Verbose error messages in production
- Missing security headers
- Overly permissive CORS

---

## Output Format

```
### Security Review

**Trust boundaries identified:** [list]
**Data flows across boundaries:** [list]

**Findings:**

| Severity | Finding | Location | Mitigation |
|---|---|---|---|
| High | [description] | [where] | [how to fix] |
| Medium | [description] | [where] | [how to fix] |

**No issues found in:** [areas reviewed with no findings]
```

---

## Limitations

- Cannot evaluate runtime security (deployment configuration, network policies)
- Cannot assess third-party library vulnerabilities without version information
- Does not replace a professional security audit for production systems
- Does not invent vulnerabilities — only reports observable issues in the provided artifact
