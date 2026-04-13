---
name: reviewing-security
description: Analyzes code for security vulnerabilities including OWASP Top 10, injection, auth flaws, secrets exposure, and crypto misuse. Use when performing a security review, security audit, or as part of code-review.
---

# Security Review

Analyze code changes for security vulnerabilities.

## Scope

- Specific files/paths if given
- `git diff` for PR/branch
- Staged/unstaged changes if no scope specified

## Checklist

1. **Injection** — SQL, command, XSS, NoSQL, LDAP/XML (unsanitized input in queries or shell calls)
2. **Auth & access control** — hardcoded credentials, missing auth checks, broken access control, weak sessions, missing CSRF
3. **Data exposure** — secrets in code/logs, missing encryption, permissive CORS, verbose errors leaking internals
4. **Crypto misuse** — weak algorithms (MD5/SHA1/DES for security), hardcoded keys/IVs, `Math.random()` for security
5. **Dependencies** — known CVEs, permissive version ranges, typosquatted packages
6. **Insecure design** — TOCTOU, mass assignment, missing rate limiting, path traversal, SSRF, insecure deserialization, unsafe file uploads

## Classification

Per finding: **Severity** (CRITICAL/HIGH/MEDIUM/LOW/INFO), **Confidence** (HIGH/MEDIUM/LOW), **CWE** if applicable.

## Output format

```
### [SEVERITY] Title
- **File**: `path:line`
- **CWE**: CWE-XXX
- **Confidence**: HIGH/MEDIUM/LOW
- **Description**: issue and impact
- **Vulnerable code**: snippet
- **Recommendation**: fix approach
- **Fixed code**: corrected snippet (if straightforward)
```

## Summary

End with severity counts table and overall assessment: **PASS** (no HIGH/CRITICAL), **WARN** (has MEDIUM), **FAIL** (has HIGH/CRITICAL).
