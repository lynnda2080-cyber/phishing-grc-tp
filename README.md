# Practical Work Report: Phishing Campaign Simulation & GRC Risk Assessment

## 1. Executive Summary & Context
Within the Governance, Risk, and Compliance (GRC) framework, human risk management constitutes a fundamental pillar of information security governance. Security awareness and technical control effectiveness must be evaluated using quantitative and qualitative risk indicators. This practical work (TP) evaluates organizational resilience against social engineering tactics by executing a controlled, ethical phishing campaign using **GoPhish** integrated with **Mailtrap** as a sandbox SMTP relay.

---

## 2. Campaign Architecture & System Configuration

The technical deployment was executed in a local virtual environment and structured across four core operational components:

| Configuration Component | Technical Details & Implementation Parameters |
| :--- | :--- |
| **Sending Profile (SMTP)** | Configured via Mailtrap SMTP Sandbox (`sandbox.smtp.mailtrap.io`, Bounce: `bounce@test.local`) to enforce safe delivery isolated from production environments. |
| **Email Template** | Designed as an urgent credential update pretext (*Reset Password*) embedding the dynamic GoPhish tracking tag `{{.URL}}` mapped to target parameters (`?rid=...`). |
| **Landing Page & Redirection** | Bound to the local GoPhish engine (`http://127.0.0.1:3333`). Configured with an automatic post-interaction redirect rule pointing to `https://www.google.com`. |
| **Target Group & Routing** | Populated with a designated recipient test batch. The base campaign URL was explicitly set to `http://127.0.0.1:3333` to ensure local HTTP listener resolution. |

---

## 3. Campaign Execution & Evidence Artifacts

### Phase A: Injection & Social Engineering Delivery
The phishing campaign was dispatched via the GoPhish console. Inspection within Mailtrap confirmed successful delivery of the email to the virtual inbox, validating the visual layout and hyperlinked "Reset Password" anchor.

![Mailtrap Inbox](screenshots/01-mailtrap.png)
*Figure 1: Mailtrap Virtual Inbox showing successful phishing email delivery.*

### Phase B: Target Interaction & Redirection Path
Clicking the "Reset Password" link initiated an HTTP GET request to `http://127.0.0.1:3333/link?rid=...` carrying the unique tracking token. The GoPhish engine processed the request and executed the diversion rule, forwarding the browser to Google.

![Landing Page Redirection](screenshots/02-redirect.png)
*Figure 2: Browser redirection to Google after target link interaction.*

### Phase C: Centralized Telemetry & Dashboard Tracking
Campaign logging was centralized in the GoPhish Dashboard. The interface recorded campaign lifecycle states and dispatch events.

![GoPhish Dashboard](screenshots/03-dashboard.png)
*Figure 3: GoPhish Campaign Dashboard displaying telemetry and operational status.*

---

## 4. Comprehensive GRC Risk Assessment

To address technical anomalies observed during execution and evaluate organizational vulnerability, a structured Risk Assessment Matrix has been constructed based on GRC risk evaluation principles:

| Risk Scenario / Observed Behavior | Root Cause Analysis | Likelihood | Impact | Risk Mitigation & Control Recommendation |
| :--- | :--- | :---: | :---: | :--- |
| **Telemetry Discrepancy**<br>*(Email Open / Click count stays at 0 despite real interaction)* | Sandbox network routing & cross-origin tracking limitations in Mailtrap local browser environments prevent live pixel callback. | Medium | Low | Deploy dedicated domain resolution (DNS/hosts mapping) and production-grade tracking endpoints during formal audits. |
| **Dispatch Bottleneck**<br>*(1 of 3 recipients delivered; error status on remaining targets)* | Free-tier Mailtrap rate-limiting and SMTP connection concurrency caps restricting multi-recipient batching. | High | Medium | Throttle campaign dispatch rates in GoPhish configuration or upgrade to enterprise SMTP relays for multi-target testing. |
| **Unvalidated Link Interaction & Immediate Redirection** | User clicked pretext link without inspecting destination URL (`?rid=...`), triggering external redirection. | High | High | Implement automated Endpoint Email Shielding (URL Sandboxing/Rewriting) and conduct targeted phishing awareness sessions. |
| **Potential Credential Exposure via Fraudulent Pretext** | Absence of Multi-Factor Authentication (MFA) allowing single-factor credential compromise. | High | Critical | Enforce mandatory FIDO2/WebAuthn Hardware MFA across all organization portals to nullify stolen password risks. |

---

## 5. Conclusion & Governance Recommendations

This simulation successfully validates the technical workflow required to audit organizational phishing risk while highlighting technical sandbox constraints. To align with **ISO/IEC 27001** and **NIST CSF** standards, the organization should:

1. **Enforce Mandatory MFA:** Mandate multi-factor authentication across all external-facing endpoints.
2. **Conduct Continuous Training:** Implement recurring phishing simulations paired with immediate remedial feedback.
3. **Deploy Technical Email Controls:** Utilize automated URL defense, domain spoofing prevention (SPF/DKIM/DMARC), and DNS filtering.
