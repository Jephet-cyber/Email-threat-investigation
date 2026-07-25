# Consolidated IOC List

All indicators of compromise identified across the 13-email investigation, with source email, indicator type, and verification result.

| Email # | IOC Type | Indicator | Where Found | Why Suspicious | Verification Result |
|---|---|---|---|---|---|
| 01 | Domain | solvexindustries.com | Sender/Return-Path | N/A — checked for due diligence | VirusTotal: 0/90+ vendors flagged, clean |
| 02 | Domain | solvex-industries-helpdesk.com | From header | Typosquat/lookalike of real solvexindustries.com domain | Pending OSINT check |
| 02 | Domain | mail-secure-verify.net | Reply-To / Return-Path | Unrelated third-party domain, doesn't match sender | Pending OSINT check |
| 02 | Domain/URL | solvex-industries-helpdesk.verify-account-secure.com/mailbox/confirm?user=aditya.rao | Embedded link in body | Company name disguised as subdomain of unrelated domain verify-account-secure.com; classic credential-phishing link structure | Pending OSINT check (do not click directly) |
| 02 | IP/Host | 193.41.77.108 (unknown-host-193.41.77.108.static-cloud.ru) | Received-From header | Russian cloud-hosting IP with no legitimate hostname, inconsistent with internal company mail infrastructure | Pending OSINT check |
| 02 | Email address | allstaff-list223@solvex-industries-helpdesk.com | Bcc field | Mass distribution address suggests phishing blast rather than targeted notice | N/A |
| 04 | — | No IOCs identified | — | Domain consistent across headers, SPF/DKIM/DMARC pass, internal source IP, no links/attachments | N/A |
| 05 | Domain | gmail-corpmail.com | From / Reply-To / Return-Path | Impersonation domain designed to look Gmail-affiliated; no SPF/DKIM/DMARC records exist | VirusTotal: no record found (inconclusive — no file/URL payload to fingerprint) |
| 05 | IP | 45.137.22.9 (smtp-relay-88.freemailhost.io, Netherlands) | Received-From header | External, out-of-org source inconsistent with legitimate executive correspondence | VirusTotal: no record found |
| 06 | — | No IOCs identified | — | Google domain consistent across headers, SPF/DKIM/DMARC pass, embedded link confirmed as genuine docs.google.com | N/A |
| 07 | Domain | micros0ft-online.com | From / Reply-To / Return-Path | Typosquat of microsoft.com using a zero in place of the letter "o" | Pending OSINT check |
| 07 | URL | login.micros0ft-online.com/secure/verify-identity?ref=aditya.rao | Embedded link in body | Credential-harvesting link on typosquat domain; `ref=` parameter personalizes/tracks the specific victim | Pending OSINT check (do not click directly) |
| 07 | IP/Host | 77.91.134.6 (host-77-91-134-6.vpn-exit.net) | Received-From header | VPN exit node of unknown origin, commonly used to mask attacker location | Pending OSINT check |
| 08 | Domain | talentbridge-recruiters.net | From / Reply-To / Return-Path | Unverified recruiting domain, no DMARC policy published; SPF/DKIM pass so authenticated for its own domain, but legitimacy of the domain itself is unconfirmed | VirusTotal (IP 156.213.44.71): 0/91 vendors flagged, clean |
| 08 | Request pattern | Requests scanned salary slip and PAN card before interview | Email body | Sensitive government ID and financial document harvesting disguised as hiring "verification"; legitimate recruiters do not require this pre-interview | N/A |
| 08 | Attachment | JD_SeniorFinanceManager_Client.zip | Email attachment | Zip attachment claiming to be a job description; unverified contents | Pending file/hash check |
| 09 | — | No IOCs identified | — | Domain consistent across From/Reply-To/Return-Path, SPF/DKIM/DMARC pass, named mail server, no payment pressure, IP checked clean on VirusTotal | VirusTotal: clean |
| 09 | Attachment | INV-88213_TallyPrime_Renewal.pdf | Email attachment | Not executed; recommend hash-checking via VirusTotal before opening in a live environment as standard practice even on low-risk emails | Pending hash check |
| 10 | Domain | yandex-mailer.com | From / Return-Path | Mismatched from Reply-To domain; part of the 3-domain inconsistency pattern | Pending OSINT check |
| 10 | Domain | outlook-verify.info | Reply-To | Unrelated third domain, doesn't match From/Return-Path | Pending OSINT check |
| 10 | IP | 185.220.101.44 (unknown-relay-node4.freehosting-mail.com, Netherlands) | Received-From header | Flagged malicious by 12/91 VirusTotal vendors; tagged "tor" (Tor exit node), indicating deliberate identity concealment | VirusTotal: 12/91 vendors flagged malicious/phishing — see `screenshots/email-10-virustotal-ip-check.jpeg` |
| 10 | URL | claim-your-prize-now.lottery-verify.info/claim?ref=44921 | Embedded link in body | Scam claim link with victim-tracking parameter, same pattern as email 07 | Pending OSINT check (do not click directly) |
| 10 | Request pattern | Requests upfront USD 250 "processing/courier fee" and copy of ID to release prize | Email body | Advance-fee scam mechanism combined with identity document harvesting | N/A |
| 11 | Header field | Reply-To: s.iyer.cfo.travel@outlook.com | Reply-To header | Mismatched from From/Return-Path domain (solvexindustries.com); replies would route externally to attacker despite SPF/DKIM/DMARC all passing — classic Reply-To hijacking technique | VirusTotal (mail.solvexindustries.com): 0/91 vendors flagged, clean — see `screenshots/email-11-virustotal-domain-check.jpeg` (note: domain itself is legitimate; the attack is the Reply-To mismatch, not the sending domain) |
| 11 | Request pattern | Requests purchase of 5 Amazon gift cards (Rs. 5,000 each) with codes sent by email | Email body | Untraceable, non-refundable payment method commonly used in BEC/gift card scams | N/A |
| 12 | — | No IOCs identified | — | Domain consistent across headers, SPF/DKIM/DMARC pass, encrypted, internal source IP, login portal link checked clean | N/A |
| 13 | Domain | shreeganesh-logistics.in | From / Reply-To / Return-Path | Domain internally consistent but not authorized to send (SPF fail) and DMARC fail; sent via unrelated shared hosting cluster rather than the vendor's own infrastructure | Pending OSINT check |
| 13 | IP/Host | 41.203.88.17 (webmail-host-4.sharedhosting-cluster.com) | Received-From header | Unrelated shared hosting cluster, inconsistent with a legitimate vendor's own mail server; came back clean on VirusTotal but noted as inconclusive given the broader header failures | VirusTotal: clean (not treated as reassuring given SPF/DMARC failures) |
| 13 | Attachment | Pending_Invoice_Challan_Details.docm | Email attachment | Macro-enabled Word document; body text explicitly instructs recipient to "enable editing/macros," a classic social-engineering script used to trigger malicious macro payloads | Pending file/hash check |
| 13 | URL | shreeganesh-logistics.in.invoice-view-secure.com/download?file=inv2207 | Embedded link in body | Trusted-looking vendor name placed as a subdomain of the actual domain, invoice-view-secure.com — same disguise technique as email 02 | Pending OSINT check (do not click directly) |
| | | | | | |

*Add one row per IOC as you work through each email. Leave "Why Suspicious" blank or write "N/A" for legitimate emails checked for due diligence.*
