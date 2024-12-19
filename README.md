# Optimized Bug Bounty Checklist for Web Applications

> Use this checklist to maintain an efficient methodology for bug bounty hunting. Remember to check off each task as you complete it. Happy hunting!

---

## Table of Contents

- [Wildcard Domain Recon](#wildcard-domain-recon)
- [Single Domain](#single-domain)
- [Information Gathering](#information-gathering)
- [Configuration Management](#configuration-management)
- [Secure Transmission](#secure-transmission)
- [Authentication](#authentication)
- [Session Management](#session-management)
- [Authorization](#authorization)
- [Data Validation](#data-validation)
- [Denial of Service](#denial-of-service)
- [Business Logic](#business-logic)
- [Cryptography](#cryptography)
- [Risky Functionality - File Uploads](#risky-functionality-file-uploads)
- [Risky Functionality - Card Payment](#risky-functionality-card-payment)
- [HTML 5](#html-5)

---

## Wildcard Domain Recon

### Tools and Commands

- **amass**: `amass enum -d <domain>`
- **subfinder**: `subfinder -d <domain>`
- **assetfinder**: `assetfinder --subs-only <domain>`
- **dnsgen**: Generate permutations: `dnsgen -f subdomains.txt > dnsgen_output.txt`
- **massdns**: `massdns -r resolvers.txt -t A -o S -w results.txt dnsgen_output.txt`
- **httprobe**: Probe for active domains: `cat results.txt | httprobe`
- **aquatone**: Screenshot active hosts: `cat httprobe_output.txt | aquatone`

### Professional Tips
- Combine outputs from different tools to ensure comprehensive coverage.
- Use `jq` to parse and filter JSON outputs for faster processing.

---

## Single Domain

### Scanning

- **Nmap**: `nmap -sC -sV -oA nmap_scan <domain>`
- **Burp crawler**: Use Burp Suite's "Spider" feature.
- **ffuf**: Directory fuzzing: `ffuf -u http://<domain>/FUZZ -w /path/to/wordlist.txt`
- **hakrawler/gau/paramspider**:
  - hakrawler: `echo <domain> | hakrawler`
  - gau: `gau <domain>`
  - paramspider: `python3 paramspider.py --domain <domain>`
- **Linkfinder**: Extract endpoints: `python3 linkfinder.py -i <url> -o cli`
- **Mobile app URLs**: Decompile APKs using JADX or apktool and search for hardcoded URLs.

### Manual Checking

- **Shodan**: `https://shodan.io/search?query=<domain>`
- **Censys**: `https://search.censys.io/`
- **Google dorks**: `site:<domain> inurl:admin`
- **Pastebin**: Search for leaks: `site:pastebin.com <domain>`
- **GitHub**: `github.com/<domain>`
- **OSINT**: Aggregate intelligence using tools like Maltego.

### Professional Tips
- Automate scans with bash scripts to save time.
- Filter out noise by prioritizing subdomains based on functionality.

---

## Information Gathering

- Manually explore the site for hidden endpoints.
- Spider/crawl: Use Burp Suite or tools like `gospider`.
- Look for sensitive files:
  - `wget https://<domain>/robots.txt`
  - `wget https://<domain>/sitemap.xml`
- Check User-Agent differences: Use Burp Suite or `curl` with `-A` flag.
- Perform fingerprinting: `whatweb <domain>` or `wappalyzer`.
- Identify technologies: Use `wafw00f` or `nuclei` templates.

### Professional Tips
- Check for debug parameters (`debug=true` or `?test=1`).
- Use Google cache and Wayback Machine to uncover historical data.

---

## Configuration Management

- Identify common URLs: `ffuf` and wordlists like SecLists.
- Check HTTP methods: `curl -X OPTIONS https://<domain>`.
- Inspect HTTP headers: Use `curl -I https://<domain>`.
- Check for backup files: `wget --spider --recursive --no-parent https://<domain>`.

### Professional Tips
- Use tools like `httpx` for bulk URL checks.
- Look for verbose server error messages revealing stack traces.

---

## Secure Transmission

- Check SSL:
  - `sslyze --regular <domain>`
  - `testssl.sh <domain>`
- Verify HTTPS usage: `curl -I https://<domain>`.
- Ensure HSTS: `curl -I https://<domain>` and check for `Strict-Transport-Security` header.

### Professional Tips
- Use SSL Labs’ online test for detailed TLS configuration insights.
- Look for mixed content warnings in the browser.

---

## Authentication

- Test for user enumeration: `ffuf` or `Burp Intruder` with error message differentiation.
- Bruteforce protection: Use Hydra or Burp Suite.
- Check password reset flow: Inspect tokens in reset emails.
- Test Multi-Factor Authentication: Try bypasses such as `OTP reuse`.

### Professional Tips
- Look for default credentials in `/admin` or `/login`.
- Capture HTTP traffic during the login process to analyze tokens.

---

## Session Management

- Inspect cookies:
  - Check `Secure` and `HttpOnly` flags.
  - Use `Burp Suite` to analyze token randomness.
- Test session termination:
  - Log out and try reusing session tokens.

### Professional Tips
- Check for predictable session IDs in cookies.
- Use `OWASP ZAP` to automate session testing.

---

## Authorization

- Path traversal: `curl https://<domain>/../../etc/passwd`
- Privilege escalation: Attempt role changes via parameter manipulation.
- Missing authorization: Try accessing restricted URLs directly.

### Professional Tips
- Map roles and permissions during testing.
- Test API endpoints for improper authorization controls.

---

## Data Validation

- SQL Injection:
  - Automated: `sqlmap -u <url>`
  - Manual: Test inputs with `' OR 1=1 --`
- XSS:
  - Reflected: Inject `<script>alert(1)</script>`
  - Stored: Test via comment or form inputs.

### Professional Tips
- Use Burp Suite’s Repeater and Intruder for manual payload testing.
- Always test with encoded and obfuscated payloads.

---

## Denial of Service

- Test rate limits: Use `ffuf` or `ab` for high-volume requests.
- Protocol DoS: Test using malformed headers with `curl` or `nmap`.

### Professional Tips
- Look for resource-intensive endpoints that can be abused.

---

## Business Logic

- Test for improper workflow enforcement:
  - E.g., Add items to cart and checkout without paying.

### Professional Tips
- Think like an end-user but also consider edge cases that developers might have missed.

---

## Cryptography

- Check encryption algorithms: `openssl` or `testssl.sh`.
- Inspect salted hashes: Look for weak salts (e.g., MD5).

### Professional Tips
- Use Burp Suite plugins for analyzing cryptographic issues.

---

## Risky Functionality - File Uploads

- Verify file type restrictions:
  - Try uploading `evil.php` disguised as `evil.jpg`.
- Check if files are executed: Access the uploaded file’s URL directly.

### Professional Tips
- Test with multiple extensions like `.php;.jpg` to bypass validation.

---

## Risky Functionality - Card Payment

- Test payment flows:
  - Try modifying the amount during requests.

### Professional Tips
- Look for hardcoded card details in API responses.

---

## HTML 5

- Web Messaging: Test for insecure `postMessage` implementations.
- CORS: Verify configuration using `curl -I -H "Origin: evil.com" <domain>`.

### Professional Tips
- Use browser developer tools to inspect HTML5 features.

---

**Sources:**
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

