# Rule Tuning and Customization

## Overview

OWASP CRS provides generic protection against common web attacks. However, production environments often require rule tuning to reduce false positives and adapt protections to application-specific behavior.

This project demonstrates:

1. Rule exclusion by ID
2. Rule exclusion by Tag
3. Custom rule creation
4. Custom IP blocking
5. Log analysis and verification

---

## 1. SQL Injection Protection

### Detection Rule

| Rule ID | Description |
|---------|-------------|
| 942100 | SQL Injection detected using libinjection |

Example payload:

```text
1' OR '1'='1
```

Result:

```text
HTTP 403 Forbidden
```

---

## 2. Cross Site Scripting Protection

### Detection Rules

| Rule ID | Description |
|---------|-------------|
| 941100 | XSS detected via libinjection |
| 941110 | Script tag vector detected |
| 941160 | HTML injection detected |

Example payload:

```html
<script>alert('XSS')</script>
```

Result:

```text
HTTP 403 Forbidden
```

---

## 3. Local File Inclusion Protection

### Detection Rules

| Rule ID | Description |
|---------|-------------|
| 930100 | Directory traversal detection |
| 930110 | URI traversal detection |
| 930120 | Sensitive file access attempt |
| 932160 | Unix shell code detection |

Example payload:

```text
../../../../etc/passwd
```

Result:

```text
HTTP 403 Forbidden
```

---

## 4. Blocking and Correlation Rules

| Rule ID | Purpose |
|---------|---------|
| 949110 | Blocking evaluation |
| 980130 | Correlation and anomaly scoring |

Example:

```text
SQLI=10
XSS=15
LFI=35
```

Requests exceeding the configured threshold were blocked automatically.

---

## 5. Rule Exclusions

### Exclusion by Rule ID

Example:

```apache
SecRuleRemoveById 942100
```

Purpose:

1. Disable a specific rule.
2. Useful for reducing false positives.

---

### Exclusion by Tag

Example:

```apache
SecRuleRemoveByTag attack-xss
```

Purpose:

1. Disable an entire category of rules.
2. Useful during testing and tuning.

---

## 6. Custom Rules

### Custom IP Blocking Rule

File:

```text
/etc/modsecurity/custom_rules.conf
```

Rule:

```apache
SecRule REMOTE_ADDR "@ipMatch 10.80.239.204" \
"id:13423432,\
phase:1,\
deny,\
log,\
status:403,\
msg:'Blocked Kali machine'"
```

Behavior:

1. Blocks requests originating from the Kali attack machine.
2. Returns HTTP 403.
3. Generates a custom log entry.

---

## 7. Lessons Learned

During development several configuration issues were encountered:

1. Duplicate rule loading due to multiple IncludeOptional directives.
2. Duplicate rule IDs causing Apache startup failures.
3. Rule ordering and phase selection affecting behavior.
4. Understanding CRS anomaly scoring and correlation rules.

---

## Conclusion

Rule tuning is a critical part of deploying a production WAF. While OWASP CRS provides strong default protection, custom rules and exclusions are often required to balance security and application usability.
