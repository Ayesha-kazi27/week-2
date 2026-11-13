<div align="center">

# 🔴 WEEK 02 — RECONNAISSANCE & NETWORK DISCOVERY

### `NETWORKWALKS` · CYBERSECURITY INTERNSHIP

<img src="https://img.shields.io/badge/FOOTPRINTING-RECONNAISSANCE-8B0000?style=for-the-badge&labelColor=111111" />
<img src="https://img.shields.io/badge/NETWORK-SCANNING-B22222?style=for-the-badge&labelColor=111111" />
<img src="https://img.shields.io/badge/KALI-LINUX-557C94?style=for-the-badge&logo=kalilinux&logoColor=white" />

*Building practical cybersecurity skills through information gathering, DNS analysis, and network discovery.*

</div>

---

## 📌 Contents

- [Overview](#-overview)
- [Lab Environment](#-lab-environment)
- [Practical 1 — Footprinting & Reconnaissance](#-practical-1--footprinting--reconnaissance-w2-pm1)
  - [WHOIS](#1--whois--domain-registration)
  - [WhatWeb](#2--whatweb--technology-fingerprinting)
  - [Nslookup](#3--nslookup--dns-resolution)
  - [cURL](#4--curl--http-header-inspection)
  - [WAFW00F](#5--wafw00f--waf-detection)
  - [DNSRecon](#6--dnsrecon--dns-enumeration)
- [Practical 2 — Network Scanning](#-practical-2--network-scanning-w2-pm5)
- [Consolidated Findings](#-consolidated-findings)
- [Learning Outcomes](#-learning-outcomes)
- [Conclusion](#-conclusion)

---

## 🎯 Overview

This repository documents the hands-on tasks completed during **Week 02** of my Cybersecurity Internship at **Networkwalks**.

The work was divided into two practical modules:

| Module | Focus | What I practised |
|:--|:--|:--|
| **W2-PM1** | Footprinting & Reconnaissance | Domain research, technology fingerprinting, HTTP analysis, and DNS enumeration |
| **W2-PM5** | Network Scanning & Discovery | Local host discovery, MAC address observation, and topology visualisation |

The objective was to understand how security professionals gather information about a target and map devices within an authorised network.

## 🧪 Lab Environment

| Parameter | Details |
|:--|:--|
| Internship | Networkwalks |
| Week | 02 |
| Operating System | Kali Linux |
| Domain Target | `networkwalks.com` |
| Resolved Domain IP | `192.232.216.135` |
| Local Network | `192.168.0.0/24` |
| Network Scan Date | 13 November 2026 |

> **Scope & authorisation:** Domain reconnaissance was performed as part of the assigned internship exercise. The Nmap/Zenmap scan targeted the local network. Scanning should only be performed on systems and networks for which permission has been granted.

---

# 🕵️ Practical 1 — Footprinting & Reconnaissance
<sub>MODULE W2-PM1 · KALI LINUX</sub>

### Objective

Collect publicly available information about the target domain, identify its web technologies, inspect HTTP response headers, detect its reported WAF, and enumerate DNS records.

### 1 · WHOIS — Domain Registration

<kbd>TOOL: WHOIS</kbd>

**Purpose:** Retrieve domain registration details, registrar information, registration dates, name servers, and domain status.

| Finding | Result |
|:--|:--|
| Creation date | 06 November 2019 |
| Last updated | 12 November 2025 |
| Registry expiry date | 06 November 2027 |
| Registrar | GoDaddy.com, LLC |
| Registrant privacy | Domains By Proxy, LLC |
| DNSSEC | Unsigned |
| Registry-listed name servers | `ns6135.hostgator.com`, `ns6136.hostgator.com` |

**Observation:** The lookup exposed the domain's registration timeline, registrar, status, and DNS information. Registrant details were privacy-protected. The registrar output also listed additional name servers, so name-server data should be checked against current authoritative DNS before drawing conclusions.

<details>
<summary>💡 What this tells us</summary>

WHOIS data can provide useful context about a domain's registration and administrative setup. Privacy-protected registration means the registrant's personal details are not directly displayed in the returned record.

</details>

### 2 · WhatWeb — Technology Fingerprinting

<kbd>TOOL: WHATWEB</kbd>

**Purpose:** Identify technologies and frameworks detectable from the target website's responses.

| Technology | Detection |
|:--|:--|
| Web server | Apache |
| CMS | WordPress 7.1.4 |
| Plugin | WordPress Download Manager 3.3.76 |
| Frontend | Bootstrap (as detected) |
| JavaScript library | jQuery 3.7.1 |
| Other | HTML5, Google Tag Manager |

**HTTP behaviour observed:**
- `http://networkwalks.com` → `301 Moved Permanently`
- `https://networkwalks.com` → `200 OK`

**Observation:** WhatWeb identified the CMS, plugin, server, frontend libraries, and other publicly detectable technologies.

### 3 · Nslookup — DNS Resolution

<kbd>TOOL: NSLOOKUP</kbd>

**Command:**

```bash
nslookup networkwalks.com
```

| Finding | Result |
|:--|:--|
| DNS resolver | `192.168.0.1` |
| Domain | `networkwalks.com` |
| Resolved IPv4 | `192.232.216.135` |
| Response | Non-authoritative answer |

**Observation:** The lookup resolved the domain name to an IPv4 address using the configured DNS resolver.

### 4 · cURL — HTTP Header Inspection

<kbd>TOOL: CURL</kbd>

**Command:**

```bash
curl -I https://networkwalks.com
```

**Purpose:** Retrieve HTTP response headers without downloading the page body.

| Header / Detail | Result |
|:--|:--|
| HTTP status | `200 OK` |
| Web server | Apache |
| Content type | `text/html; charset=UTF-8` |
| WordPress API reference | `/wp-json/` |
| Cookie | `__wpdm_client` |
| Cookie attributes | `Secure`, `HttpOnly` |
| Other headers | `Permissions-Policy`, `Referrer-Policy`, cache headers |

**Observation:** The response exposed server details, a WordPress API reference, cookie attributes, caching information, and browser policy headers.

### 5 · WAFW00F — WAF Detection

<kbd>TOOL: WAFW00F v2.4.2</kbd>

**Command:**

```bash
wafw00f networkwalks.com
```

| Finding | Result |
|:--|:--|
| WAF detected | Yes |
| Identified technology | ModSecurity |
| Vendor / project | SpiderLabs |
| Requests made | 2 |

**Observation:** WAFW00F identified ModSecurity (SpiderLabs) as the WAF associated with the target. This is a fingerprinting result and does not assess the effectiveness of its configuration.

### 6 · DNSRecon — DNS Enumeration

<kbd>TOOL: DNSRECON</kbd>

**Command:**

```bash
dnsrecon -d networkwalks.com
```

**Purpose:** Enumerate DNS records and identify domain, mail, and service-related information.

| Record / Finding | Result |
|:--|:--|
| SOA | `ns6135.hostgator.com` — `50.87.144.87` |
| NS | `ns6135.hostgator.com` — `50.87.144.87` |
| NS | `ns6136.hostgator.com` — `192.232.216.131` |
| A | `192.232.216.135` |
| MX | `mail.networkwalks.com` — `192.232.216.135` |
| TXT | Google site verification and SPF policy |
| SRV | 8 email autodiscovery records |
| BIND version string | `9.16.23-RH` (reported by name servers) |
| DNSSEC query | No answer received |

**Observation:** DNSRecon identified SOA, NS, A, MX, TXT, and SRV information. It also reported a BIND version string and cPanel email autodiscovery records.

> **Interpretation note:** A DNSSEC query returning no answer does not, by itself, constitute a complete DNSSEC validation. A detected software version string should also be independently verified before drawing conclusions about the software currently running.

---

# 🌐 Practical 2 — Network Scanning & Discovery
<sub>MODULE W2-PM5 · NMAP + ZENMAP</sub>

### Objective

Discover active devices on the local network, record available IP and MAC address information, and visualise discovered hosts using Zenmap.

### Scan Configuration

| Parameter | Result |
|:--|:--|
| Tool | Nmap through Zenmap |
| Profile | Ping scan |
| Network | `192.168.0.0/24` |
| IP addresses scanned | **256** |
| Active hosts found | **3** |
| Scan duration | **2.92 seconds** |
| Scan date shown in Zenmap | 13 November 2026 |

### Command

```bash
nmap -sn 192.168.0.103/24
```

- `nmap` — network discovery and security auditing tool.
- `-sn` — host discovery without a port scan.
- `192.168.0.103/24` — target entered in the Zenmap scan field; the output reports a 256-address `/24` scan. The conventional network address for this subnet is `192.168.0.0/24`.

### Active Hosts Discovered

| No. | IP Address | MAC Address | Vendor / Result |
|:--:|:--|:--|:--|
| 01 | `192.168.0.1` | `98:25:4A:25:FC:8E` | TP-Link Systems |
| 02 | `192.168.0.103` | Not displayed | No MAC shown |
| 03 | `192.168.0.105` | `16:F2:CE:84:C8:C3` | Unknown |

### Scan Snapshot

<div align="center">

| Hosts discovered | Addresses scanned | Scan time |
|:--:|:--:|:--:|
| **3** | **256** | **2.92 s** |

</div>

**Observations:**
- Three active hosts were discovered.
- MAC address information was displayed for two hosts.
- One host did not display a MAC address in the scan output.
- Zenmap's **Topology** view was used to visualise the discovered hosts.

<details>
<summary>🧭 Understanding the topology view</summary>

Zenmap provides a graphical representation of hosts discovered by Nmap. The topology view helps make scan results easier to interpret, but it should not be treated as proof of exact physical connections or network layout.

</details>

---

## 📊 Consolidated Findings

| Tool | Activity | Main result |
|:--|:--|:--|
| WHOIS | Domain registration lookup | Registrar, dates, status, and name-server information |
| WhatWeb | Technology fingerprinting | Apache, WordPress, plugin, and frontend detections |
| Nslookup | DNS resolution | `192.232.216.135` |
| cURL | HTTP header inspection | `200 OK`, Apache, WordPress API reference, and response headers |
| WAFW00F | WAF fingerprinting | ModSecurity (SpiderLabs) detected |
| DNSRecon | DNS enumeration | SOA, NS, A, MX, TXT, and SRV records |
| Nmap | Host discovery | 3 active hosts found |
| Zenmap | Network visualisation | Topology view generated |

---

## 🧠 Learning Outcomes

During these practicals, I gained hands-on experience with:

- Domain registration research using WHOIS.
- Website technology fingerprinting using WhatWeb.
- DNS resolution using Nslookup.
- HTTP response-header inspection using cURL.
- WAF identification using WAFW00F.
- DNS record enumeration using DNSRecon.
- Local host discovery using Nmap.
- Network topology visualisation using Zenmap.
- Documenting findings and distinguishing observations from confirmed vulnerabilities.

## 🏁 Conclusion

Week 02 strengthened my understanding of the initial stages of a cybersecurity assessment — **footprinting, reconnaissance, DNS enumeration, technology identification, and network discovery**.

Working with these tools helped me understand how to collect and interpret information about a domain and identify active devices within a network. These exercises reinforced the importance of accurate documentation, careful interpretation of scan results, and testing only within an authorised scope.

---

<div align="center">

### `END OF WEEK 02 REPORT`

**Ayesha Kazi**  
Cybersecurity Intern · Networkwalks

<sub>W2-PM1 — Footprinting & Reconnaissance · W2-PM5 — Network Scanning</sub>

</div>
