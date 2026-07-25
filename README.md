# Email Threat Investigation & Analysis

## Overview
Investigation of 12 simulated emails to determine legitimacy, extract Indicators of Compromise (IOCs), and classify risk — using SOC analyst triage methodology. This project simulates real-world Tier 1 SOC phishing/email abuse investigation workflows.

## Objective
Demonstrate the ability to investigate suspicious emails end-to-end: header analysis, sender/domain verification, link and attachment inspection, social engineering detection, IOC extraction, OSINT cross-verification, and risk classification with evidence-based justification.

## Methodology
For each email, the following process was applied:

1. **Header Analysis** — sender vs. display name, Reply-To/Return-Path anomalies, domain spoofing or lookalike domains, SPF/DKIM/DMARC results
2. **Link & Attachment Inspection** — destination vs. displayed URL text (never clicked directly), file type/naming/macro risk
3. **Social Engineering Assessment** — urgency, fear, authority, or reward-based language
4. **IOC Extraction** — suspicious domains, URLs, IPs, file names/hashes, with notes on where each was found and why it's suspicious
5. **Cross-Verification (OSINT)** — suspicious indicators checked against VirusTotal, urlscan.io, MXToolbox, and Google Safe Browsing
6. **Risk Classification** — each email rated Low / Medium / High / Critical, justified by evidence rather than assumption

## Tools Used
- VirusTotal — domain/URL/file reputation checks
- urlscan.io — safe URL detonation and redirect chain analysis
- MXToolbox — mail server and DNS record lookups
- Google Safe Browsing — malicious site verification

## Sample Finding

**Email 02 — "URGENT: Your Mailbox Storage is FULL – Verify Now to Avoid Suspension"**
- **Verdict:** Malicious — Credential Phishing
- **Key evidence:**
  - Three inconsistent domains across From (`solvex-industries-helpdesk.com`, a typosquat of the real `solvexindustries.com`), Reply-To, and Return-Path (`mail-secure-verify.net`, unrelated to either)
  - SPF, DKIM, and DMARC all Fail — DMARC shows "Reject policy overridden," meaning the domain's own enforcement policy failed to block delivery. This is because the attacker used self-owned lookalike domains rather than directly spoofing the real solvexindustries.com domain, so the real domain's DMARC policy never applied to this message in the first place
  - Received-From header traces to an unnamed Russian cloud-hosting IP (`193.41.77.108.static-cloud.ru`), inconsistent with the internal IP seen in legitimate company mail
  - No TLS encryption
  - Bcc'd to a mass distribution address (`allstaff-list223@...`), indicating a phishing blast rather than a targeted 1-to-1 IT notice
  - Embedded credential-harvesting link disguises the real destination domain by placing the trustworthy-looking company name as a subdomain: `solvex-industries-helpdesk.verify-account-secure.com` — the actual domain is `verify-account-secure.com`
  - Urgency/fear-based language: "URGENT," "IMMEDIATELY," "final notice," threat of permanent data loss within 24 hours
- **Confirmation:** Domain/link flagged for OSINT verification via VirusTotal/urlscan.io (link not clicked directly, per standard SOC handling procedure)

**Email 05 — "Confidential – Immediate Wire Transfer Required (Time Sensitive)"**
- **Verdict:** Malicious — Business Email Compromise (CEO Fraud)
- **Key evidence:**
  - Sender domain `gmail-corpmail.com` impersonates a legitimate corporate identity by pairing a real-sounding executive name/title with a domain designed to look Gmail-affiliated — this is domain impersonation rather than direct spoofing
  - No SPF, DKIM, or DMARC records exist for the domain at all, and no TLS encryption
  - Source IP is external and out-of-org (Netherlands-based SMTP relay), inconsistent with legitimate internal executive correspondence
  - No malicious link or attachment — the attack is pure social engineering: authority (claims to be Managing Director), urgency ("within the next hour"), secrecy/isolation ("do not discuss this with Priya or anyone in Accounts"), and an unreachable sender ("cannot take calls right now") to block verification
  - VirusTotal returned no record for the sending IP — noted in the report as inconclusive rather than reassuring, since this attack has no file/URL payload to fingerprint
- **Confirmation:** No technical indicator confirms maliciousness alone; classification is based on the absence of authentication combined with the BEC social-engineering pattern (authority + urgency + secrecy + unusual financial request)

## Findings Summary

| Email # | Subject | Verdict | Key Indicators | Risk Level |
|---|---|---|---|---|
| 01 | Password Expiry Reminder | Legitimate | Domain consistency, SPF/DKIM/DMARC pass, internal IP, no malicious links/attachments, VirusTotal clean | Low |
| 02 | URGENT: Your Mailbox Storage is FULL – Verify Now to Avoid Suspension | Malicious (Credential Phishing) | Typosquat domain, 3 mismatched domains (From/Reply-To/Return-Path), SPF/DKIM/DMARC fail, unknown Russian cloud IP source, no TLS, mass Bcc, subdomain-disguised phishing link, urgency language | Critical |
| 04 | Revised Leave & Work-From-Home Policy – Effective 15 July 2026 | Legitimate | Domain consistency, SPF/DKIM/DMARC pass, internal IP, no links/attachments, no IOCs identified | Low |
| 05 | Confidential – Immediate Wire Transfer Required (Time Sensitive) | Malicious (Business Email Compromise / CEO Fraud) | Domain impersonation (gmail-corpmail.com), no SPF/DKIM/DMARC, external out-of-org IP, no encryption, authority + urgency + secrecy social engineering pattern, unusual wire transfer request | Critical |
| 06 | 'Q1_Vendor_Compliance_Report.xlsx' has been shared with you | Legitimate | Consistent Google domain across headers, SPF/DKIM/DMARC pass, genuine Google IP range, embedded link confirmed as real docs.google.com domain, mild non-manipulative tone | Low |
| 07 | Unusual sign-in activity detected on your Microsoft account | Malicious (Credential Phishing / Fake Security Alert) | Typosquat domain (micros0ft-online.com using zero for "o"), SPF/DKIM/DMARC fail, no encryption, VPN exit-node source of unknown origin, personalized phishing link with victim-tracking parameter, fear/urgency social engineering with fabricated specific details | Critical |
| 08 | Exciting Career Opportunity – Senior Finance Role (Immediate Response Needed) | Malicious (Job Scam / PII Harvesting) | SPF/DKIM pass but DMARC absent (unverified domain, not a technical failure), requests scanned salary slip and government ID (PAN card) before any interview, too-good-to-be-true salary framing, urgency to reply within 24 hours, unverified recruiting domain | Critical |
| 09 | Your TallyPrime Cloud Subscription Renewal Invoice – INV-88213 | Legitimate | Return-Path matches Reply-To/From (tallysolutions.com), SPF/DKIM/DMARC pass, named mail server (not raw IP/hosting provider), no payment pressure or urgency ("no bank details have changed"), IP checked clean on VirusTotal, attachment not executed — recommend hash-checking via VirusTotal before opening as standard practice | Low |
| 10 | CONGRATULATIONS!!! YOU HAVE WON USD 1,000,000 IN THE INTERNATIONAL EMAIL LOTTERY | Malicious (Advance-Fee Lottery Scam / ID Harvesting) | Three mismatched domains across From/Reply-To/Return-Path, SPF/DKIM/DMARC fail, no encryption, IP flagged malicious by 12/91 VirusTotal vendors and tagged "tor" (Tor exit node), requests upfront processing fee and copy of ID to release fake prize, mass-blasted to "undisclosed-recipients," scam link with tracking parameter, 48-hour urgency/forfeiture threat | Critical |
| 11 | Quick request before my flight | Malicious (Business Email Compromise / Reply-To Hijacking) | SPF/DKIM/DMARC all pass (authenticated via solvexindustries.com), but Reply-To routes externally to a different domain (outlook.com) than From/Return-Path, so replies would go to the attacker despite passing authentication; requests untraceable gift card purchase, urgency ("20 minutes"), isolation tactic ("don't call the office landline"), impersonates CFO authority; domain checked clean on VirusTotal | Critical |
| 12 | Reminder: Mandatory Security Awareness Training – Due This Friday | Legitimate | Domain consistent across From/Reply-To/Return-Path, SPF/DKIM/DMARC pass, encrypted (TLS 1.3), internal source IP, login portal link checked clean, calm routine internal tone with normal deadline and fallback process | Low |
| 13 | Pending Invoice – Please Review & Process on Priority | Malicious (Vendor Impersonation / Malicious Macro Document) | SPF fail (domain not authorized to send), no DKIM, DMARC fail, no encryption, source is an unrelated shared hosting cluster rather than the vendor's own mail servers, macro-enabled .docm attachment with body text explicitly instructing the recipient to enable macros/editing, embedded link disguises real domain (invoice-view-secure.com) behind a trusted-looking subdomain | Critical |

## Consolidated IOC List
See [`ioc-list.md`](./ioc-list.md) for the full list of extracted indicators across all 12 emails.

## Recommendations
Based on the patterns identified across this investigation, the following controls are recommended:

- **Enforce DMARC at `p=reject`** on the organization's own domain, and monitor for lookalike/typosquat domains registered against the company name
- **Security awareness training** focused on recognizing urgency/authority/secrecy social engineering patterns (BEC), not just obvious spam — several malicious emails in this batch passed technical authentication entirely
- **Disable macros by default** on documents received from external senders, and flag any attachment/email combination that explicitly instructs the recipient to "enable macros" or "enable editing"
- **Email gateway URL rewriting and sandboxed detonation** for embedded links, particularly to catch subdomain-disguise techniques (e.g., `trusted-name.attacker-domain.com`)
- **Out-of-band verification process** for any financial request (wire transfers, gift cards, invoice payments) above a defined threshold, especially when the sender claims to be unreachable by phone
- **Reply-To monitoring** at the email gateway level to flag messages where Reply-To domain differs from the From/Return-Path domain, since this bypasses SPF/DKIM/DMARC entirely
- **PII handling policy reminders** for employees, particularly around recruitment/HR-themed emails requesting government ID or financial documents before any verified hiring step

## Key Takeaway
This investigation reinforced that technical authentication (SPF/DKIM/DMARC) and clean OSINT results are necessary but not sufficient to clear an email — several of the most dangerous emails in this batch (Reply-To hijacking, BEC wire fraud, job-scam PII harvesting) passed every technical check or came back clean on VirusTotal, because the attack lived in the social engineering and request pattern rather than in a bad domain or file. The most reliable signal across this batch wasn't any single header field, but whether the request itself — an urgent wire transfer, a scanned ID, an "enable macros" instruction — made sense coming from who it claimed to be from.

---
*This project uses simulated training data and does not reference any real organization or individual.*
