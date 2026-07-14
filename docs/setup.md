# Setup Guide

## Environment

| Component | Version |
|-----------|---------|
| Ubuntu | 24.04 |
| Apache | 2.4.66 |
| ModSecurity | 2.9.12 |
| OWASP CRS | 3.3.8 |
| DVWA | Latest |
| OpenSSL | 3.5.5 |

---

## 1. Install Apache

```bash
sudo apt update
sudo apt install apache2 -y
```

Verify installation:

```bash
sudo systemctl status apache2
```

---

## 2. Install ModSecurity

```bash
sudo apt install libapache2-mod-security2 -y
```

Verify module:

```bash
sudo apachectl -M | grep security
```

Expected output:

```text
security2_module (shared)
```

---

## 3. Enable ModSecurity Engine

Copy the recommended configuration:

```bash
sudo cp /etc/modsecurity/modsecurity.conf-recommended \
/etc/modsecurity/modsecurity.conf
```

Edit configuration:

```bash
sudo nano /etc/modsecurity/modsecurity.conf
```

Change:

```text
SecRuleEngine DetectionOnly
```

to:

```text
SecRuleEngine On
```

---

## 4. Install OWASP CRS

Verify installation:

```bash
ls /usr/share/modsecurity-crs
```

Expected output:

```text
owasp-crs.load
rules/
util/
```

---

## 5. Verify CRS Loading

Check:

```bash
cat /etc/apache2/mods-enabled/security2.conf
```

Ensure:

```text
IncludeOptional /usr/share/modsecurity-crs/*.load
```

exists.

---

## 6. Configure DVWA

Clone DVWA:

```bash
git clone https://github.com/digininja/DVWA.git
```

Move to web root:

```bash
sudo mv DVWA /var/www/html/
```

Configure database credentials:

```bash
sudo nano /var/www/html/DVWA/config/config.inc.php
```

---

## 7. Configure HTTPS

Generate certificate:

```bash
sudo openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 \
-keyout /etc/ssl/dvwa/dvwa.key \
-out /etc/ssl/dvwa/dvwa.crt
```

Enable SSL:

```bash
sudo a2enmod ssl
sudo systemctl restart apache2
```

---

## 8. Add Custom Rules

Custom rules location:

```text
/etc/modsecurity/custom_rules.conf
```

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

## 9. Restart Apache

```bash
sudo apachectl configtest
sudo systemctl restart apache2
```

---

## 10. Test the WAF

SQL Injection:

```text
1' OR '1'='1
```

XSS:

```html
<script>alert('XSS')</script>
```

LFI:

```text
../../../../etc/passwd
```

Expected response:

```text
403 Forbidden
```
