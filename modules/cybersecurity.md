# Cybersecurity Module

## Purpose

Evaluates the security posture of code, architectures, and designs. Identifies vulnerabilities, trust boundary violations, and weak security assumptions. Recommends practical mitigations.

---

## Responsibilities

- Review code for security vulnerabilities
- Evaluate authentication and authorisation designs
- Identify trust boundary violations
- Recommend security controls appropriate to the risk level
- Evaluate cryptographic design choices
- Contribute to threat modelling

---

## Activation Criteria

Activate when the request involves:
- Authentication or authorisation design
- User input handling
- Data storage (especially sensitive data)
- Network communication
- Cryptography or secrets management
- File system operations
- Production code deployment
- API design with public endpoints

---

## Inputs

- Code or design to review
- Deployment context (public internet, internal, etc.)
- Sensitivity of data handled
- Trust model (who are the actors and what do they trust?)
- Regulatory or compliance requirements

---

## Outputs

- Prioritised vulnerability findings
- Severity classifications (Critical / High / Medium / Low)
- Specific mitigations for each finding
- Security architecture recommendations

---

## Dependencies

- `engines/threat-model-engine.md` — structured threat analysis
- `modules/architecture.md` — security architecture design
- `modules/api-design.md` — API security patterns

---

## Security Principles Applied

**Defence in Depth** — multiple independent controls; no single point of failure.

**Least Privilege** — every component has only the permissions it needs.

**Fail Secure** — on error, default to denying access, not granting it.

**Zero Trust** — validate all requests regardless of network origin.

**Separation of Concerns** — authentication, authorisation, and business logic are separate.

---

## Vulnerability Taxonomy

### Injection
| Type | Pattern | Mitigation |
|---|---|---|
| SQL Injection | String concatenation in queries | Parameterised queries / prepared statements |
| Command Injection | User input in shell commands | Avoid shell calls; use library APIs |
| XSS | Unescaped output in HTML | Context-aware output encoding |
| Path Traversal | User-controlled file paths | Allowlist paths; canonicalise before checking |
| SSRF | User-controlled URLs | Allowlist destinations; block internal ranges |

### Authentication
| Weakness | Mitigation |
|---|---|
| Weak password policy | Enforce minimum entropy; check against breach databases |
| Missing MFA | Require MFA for sensitive operations |
| Insecure token storage | HttpOnly, Secure cookies for web; Keychain/Keystore for mobile |
| Missing token expiry | Short-lived access tokens; rotating refresh tokens |
| Predictable tokens | Cryptographically random token generation |

### Authorisation
| Weakness | Mitigation |
|---|---|
| Missing authorisation checks | Check permissions on every protected endpoint |
| IDOR | Use opaque references; verify ownership server-side |
| Horizontal privilege escalation | Verify resource ownership, not just authentication |
| Overpermissioned tokens | Scope tokens to minimum required permissions |

### Cryptography
| Weakness | Mitigation |
|---|---|
| Hardcoded secrets | Environment variables; secrets manager |
| Weak hashing for passwords | bcrypt, Argon2, or scrypt with appropriate cost factor |
| ECB mode | Use GCM or CBC with authentication |
| MD5 / SHA1 for integrity | SHA-256 or above |
| Missing TLS | Enforce HTTPS; HSTS header |

---

## Examples

### Example 1 — SQL Injection Review

**Vulnerable code:**
```python
query = f"SELECT * FROM users WHERE email = '{user_email}'"
cursor.execute(query)
```

**Finding:** High — SQL injection. User-controlled input is concatenated directly into a SQL query.

**Fix:**
```python
query = "SELECT * FROM users WHERE email = %s"
cursor.execute(query, (user_email,))
```

---

## Best Practices

- Check trust boundaries before reviewing individual lines of code
- Start with authentication and authorisation — these have the highest impact
- Prioritise findings by exploitability and impact, not just theoretical severity
- Recommend mitigations that fit the team's capability and context
- Never invent vulnerabilities — only report observable issues

---

## Common Mistakes

- Reporting theoretical vulnerabilities without evidence they exist in the code
- Missing authorisation checks by focusing only on authentication
- Recommending overly complex security controls for low-risk scenarios
- Ignoring the deployment context (internal tool vs public API)

---

## Limitations

- Cannot evaluate runtime security (WAF, network policies, OS hardening)
- Cannot assess third-party dependencies without version information
- Does not replace a professional penetration test for production systems
- Cryptographic recommendations are general; a cryptographer should review novel schemes
