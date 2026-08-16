# Web Application Security Assessment with OWASP ZAP

## Overview

This project documents a controlled web application security assessment of **OWASP Juice Shop**, an intentionally vulnerable application, using **OWASP ZAP** in a Kali Linux lab environment.

The objective was to move beyond simply running an automated scanner by following a structured assessment workflow: deploy the target, verify connectivity, discover application content, perform active vulnerability scanning, review individual findings, evaluate security impact, and document remediation guidance.

> **Scope:** All testing was performed against a locally hosted OWASP Juice Shop Docker container in an isolated lab environment.

---

## Lab Environment

- Kali Linux
- OWASP ZAP 2.17.0
- OWASP Juice Shop
- Docker
- Firefox
- VirtualBox
- Target: `http://127.0.0.1:3000`

Juice Shop was deployed locally using Docker and verified as accessible before scanning. ZAP was then used to spider the application and perform an active security scan against the discovered attack surface.

---

## Assessment Methodology

### 1. Target Deployment and Validation

OWASP Juice Shop was deployed as a Docker container and exposed locally on TCP port 3000.

Connectivity was validated independently before scanning to confirm that the application was responding normally.

### 2. Application Discovery

The target was added to ZAP and crawled using the traditional Spider. This populated the site tree and discovered application resources without relying on browser automation.

### 3. Active Vulnerability Scan

After discovery completed, an Active Scan was launched against the local Juice Shop target. ZAP tested the discovered application surface and generated alerts categorized by risk and confidence.

### 4. Finding Review

Individual alerts were reviewed rather than treating the automated output as a final conclusion. Evidence, affected URLs, response headers, CWE/WASC references, risk ratings, and remediation guidance were examined for the principal findings.

### 5. Reporting

A formal ZAP HTML assessment report was generated and archived in this repository along with screenshots of the scan results and selected findings.

---

## Scan Summary

The completed scan produced the following alert categories:

| Severity | Alert Categories |
| --- | ---: |
| High | 0 |
| Medium | 2 |
| Low | 1 |
| Informational | 3 |

Observed alert categories included:

- Content Security Policy (CSP) Header Not Set
- Cross-Domain Misconfiguration
- Timestamp Disclosure - Unix
- Information Disclosure - Suspicious Comments
- Modern Web Application
- User Agent Fuzzer

The absence of a High-risk alert in this scan was not interpreted as proof that the application was secure. Automated scanning provides coverage of the application surface reached by the scanner and should be combined with validation and additional testing where appropriate.

---

## Key Findings

### 1. Content Security Policy Header Not Set

**Risk:** Medium  
**Confidence:** High  
**CWE:** 693  
**WASC:** 15

ZAP identified responses that did not define a Content Security Policy header. CSP provides an additional browser-side security layer by restricting which sources may load or execute content.

**Security impact:**

A missing CSP reduces defense-in-depth against client-side content injection. It does not by itself prove that an exploitable cross-site scripting vulnerability exists, but it removes a control that can reduce the impact of certain injection attacks.

**Recommended remediation:**

Define and test a restrictive Content Security Policy appropriate to the application's required scripts, styles, images, frames, and other resources. Avoid unnecessarily broad source directives.

![CSP Header Finding](screenshots/02-csp-header-not-set.png)

---

### 2. Cross-Domain Misconfiguration

**Risk:** Medium  
**Confidence:** Medium  
**Evidence:** `Access-Control-Allow-Origin: *`  
**CWE:** 264  
**WASC:** 14

ZAP observed a permissive cross-origin response policy using the wildcard `Access-Control-Allow-Origin: *` header.

**Security impact:**

Overly permissive CORS configuration can allow untrusted origins to read resources that were intended for a more restricted set of web origins. Actual impact depends on the sensitivity of the affected resource and whether credentials or other authorization mechanisms are involved.

**Recommended remediation:**

Restrict allowed origins to explicitly trusted domains where cross-origin access is required. Review sensitive endpoints individually rather than applying broad cross-origin permissions by default.

![Cross-Domain Finding](screenshots/03-cross-domain-misconfiguration.png)

---

### 3. Timestamp Disclosure

**Risk:** Low

ZAP identified Unix timestamp information within application responses.

**Security impact:**

Timestamp disclosure is generally low risk but can provide additional environmental or application information during reconnaissance. Such information can sometimes help an attacker correlate application behavior, versions, events, or generated resources.

**Recommended remediation:**

Avoid exposing unnecessary internal timing or metadata where it provides no application value. Evaluate disclosures in context rather than treating all timestamps as vulnerabilities.

---

## Scan Evidence

The ZAP interface documented the completed assessment and resulting alert categories.

![ZAP Scan Results](screenshots/01-scan-results.png)

A formal HTML assessment report was also generated from ZAP.

![ZAP Report](screenshots/04-zap-report-cover.png.png)

The archived report and supporting assets are available in the [`reports`](reports/) directory.

---

## Security Analysis

This assessment reinforced several important application-security principles:

- Automated scanners identify potential weaknesses but their findings require analyst interpretation.
- Risk ratings should be considered alongside the affected resource, evidence, confidence, and application context.
- Security headers such as CSP provide important defense-in-depth controls.
- CORS configuration should follow least-privilege principles rather than allowing unnecessary cross-origin access.
- Low-risk information disclosure can still contribute to reconnaissance even when it is not directly exploitable.
- A scan returning no High-risk alerts does not establish that an application is free of serious vulnerabilities.

---

## Tools and Skills Demonstrated

- OWASP ZAP
- Dynamic Application Security Testing (DAST)
- Web application reconnaissance
- Spidering / content discovery
- Active vulnerability scanning
- HTTP request and response analysis
- Security header analysis
- CORS analysis
- CWE / WASC interpretation
- Vulnerability triage
- Risk analysis
- Remediation recommendations
- Docker
- Kali Linux
- Technical security reporting

---

## Project Artifacts

```text
web-application-security-assessment/
├── README.md
├── reports/
│   └── juice-shop-zap-report.zip
└── screenshots/
    ├── 01-scan-results.png
    ├── 02-csp-header-not-set.png
    ├── 03-cross-domain-misconfiguration.png
    └── 04-zap-report-cover.png.png
```

---

## Conclusion

This project demonstrates a repeatable web application assessment workflow using an intentionally vulnerable target in an isolated environment. The emphasis was not simply on generating scanner alerts, but on validating the target, controlling the scan process, reviewing evidence, interpreting risk, and producing actionable remediation guidance.
