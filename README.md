# DWA-WAF-LAB

Application-layer defense lab using ModSecurity and OWASP CRS against OWASP Top 10 attacks.

---

## Overview

This project demonstrates the deployment and tuning of a Web Application Firewall (WAF) using ModSecurity and the OWASP Core Rule Set (CRS) in front of DVWA (Damn Vulnerable Web Application).

The objective was to understand how WAFs detect, score, correlate and block common web attacks.

---

## Technologies Used

1. Ubuntu 24.04
2. Apache 2.4
3. ModSecurity 2.9
4. OWASP CRS 3.3.8
5. DVWA
6. MariaDB
7. Kali Linux

---

## Architecture

![Architecture](screenshots/01_architecture.png)

---

## Implemented Protections

1. SQL Injection (SQLi)
2. Cross Site Scripting (XSS)
3. Local File Inclusion (LFI)
4. Protocol Enforcement
5. Custom IP Blocking
6. Rule Exclusions and Tuning

---

## Demonstrated OWASP CRS Rules

| Category | Rule IDs |
|----------|----------|
| SQL Injection | 942100 |
| XSS | 941100, 941110, 941160 |
| LFI | 930100, 930110, 930120 |
| Blocking | 949110 |
| Correlation | 980130 |
| Protocol Enforcement | 920350 |

---

## Custom Rules

Custom ModSecurity rules were implemented for:

1. IP based blocking
2. Rule exclusions
3. Rule tuning

Example:

```apache
SecRule REMOTE_ADDR "@ipMatch 10.80.239.204" \
"id:13423432,\
phase:1,\
deny,\
log,\
status:403,\
msg:'Blocked Kali machine'"
```

---

## Repository Structure

```text
configs/      -> ModSecurity configuration files
docs/         -> Documentation
payloads/     -> Test payloads
screenshots/  -> Attack and detection screenshots
```

---

## Documentation

- `docs/setup.md`
- `docs/architecture.md`
- `docs/rule_tuning.md`

---

## License

This project is distributed under the MIT License.
