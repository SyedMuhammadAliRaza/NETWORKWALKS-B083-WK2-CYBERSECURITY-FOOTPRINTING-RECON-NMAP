# [NETWORKWALKS-B083-WK2-CYBERSECURITY](https://github.com/SyedMuhammadAliRaza/NETWORKWALKS-B083-WK2-CYBERSECURITY) — Reconnaissance, OSINT & Network Scanning

![Cybersecurity](https://img.shields.io/badge/Cybersecurity-Ethical%20Hacking-red)
![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-blue)
![Batch](https://img.shields.io/badge/Batch-B083-orange)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## 📌 Overview

This repository documents **Week 2** of the Cybersecurity & Ethical Hacking Internship at **Networkwalks Technologies (Batch B083)**, covering the two foundational phases of any penetration test: **passive reconnaissance (footprinting)** and **active network scanning**.

Across three hands-on project modules, this week focused on building a complete intelligence profile of a live target using industry-standard Kali Linux tools, followed by mapping a live local network using Nmap/Zenmap.

| Module | Topic | Tools Used |
|--------|-------|------------|
| PM1 | Footprinting with Multiple Kali Tools | whois, whatweb, nslookup, curl, wafw00f, dnsrecon |
| PM4 | OSINT Footprinting with theHarvester | theHarvester |
| PM5 | Active Network Scanning | Zenmap (Nmap GUI) |

> ⚠️ **Disclaimer:** All activities in this repository were performed strictly for educational purposes within an authorized lab environment, against a domain the internship program has explicitly permitted for testing (`networkwalks.com`), or on the local home/lab network under the tester's own control. No unauthorized access, intrusive testing, or exploitation was performed at any stage.

---

## 🎯 Objectives

- Apply **passive reconnaissance** techniques used by attackers and defenders before any real-world engagement
- Extract domain registration data, web technology fingerprints, DNS records, and WAF presence for a live target
- Gather open-source intelligence (OSINT) — emails and sub-domains — using theHarvester
- Perform active host discovery, live-host enumeration, and network topology mapping using Zenmap/Nmap on a real subnet

---

## 🧩 Module 1: Footprinting with Multiple Kali Tools

**Target:** `networkwalks.com`

### Task 1 — WHOIS Lookup
```bash
whois networkwalks.com
```
![whois 1](<whois 1.png>)
![whois 2](<whois 2.png>)

**Finding:** Domain registered via GoDaddy, hosted on HostGator name servers (`NS6135` / `NS6136.HOSTGATOR.COM`), registered 2019-11-06, expires 2027-11-06.

---

### Task 2 — WhatWeb Fingerprinting
```bash
whatweb networkwalks.com
```
![whatweb](<whatweb.png>)

**Finding:** Site runs Apache, WordPress 7.1.1, Bootstrap 7.1.1, WP Download Manager 3.3.58, hosted at IP `192.232.216.135`.

---

### Task 3 — DNS Resolution
```bash
nslookup networkwalks.com
```
![nslookup](<nslookup.png>)

**Finding:** Resolved IP address — `192.232.216.135`.

---

### Task 4 — HTTP Header Inspection
```bash
curl -I https://networkwalks.com
```
![Curl -I](<Curl -I.png>)

**Finding:** Server exposes the WordPress REST API endpoint (`/wp-json/`), running Apache, with nginx-level caching.

---

### Task 5 — WAF Detection
```bash
wafw00f networkwalks.com
```
![wafw00f](<wafw00f.png>)

**Finding:** Site is protected by **ModSecurity (SpiderLabs)** WAF.

---

### Task 6 — DNS Enumeration
```bash
dnsrecon -d networkwalks.com
```
![DNS Recon](<Dnsrecon ss.png>)

**Finding:** Enumerated 8 DNS records — SOA, NS (HostGator, running BIND 9.16.23-RH), MX (`mail.networkwalks.com`), SPF/TXT policy records, and 6 SRV records pointing to cPanel's autodiscover email service.

---

## 🧩 Module 2: OSINT Footprinting with theHarvester

**Target:** `networkwalks.com`

![Harvester 1](<Harvester 1.png>)


### Task 1 — Baidu Source, Limit 1000
```bash
theHarvester -d networkwalks.com -l 1000 -b baidu
```
![Harvester 2](<Harvester 2.png>)


### Task 2 — All Sources, Limit 50
```bash
theHarvester -d networkwalks.com -l 50 -b all
```
![Harvester 3](<Harvester 3.png>)

**Finding:** Gathered publicly available emails and sub-domains associated with `networkwalks.com` across multiple free OSINT sources.

---

## 🔍 Security Observations (Passive Recon Findings)

While no exploitation or intrusive testing was performed, the reconnaissance phase surfaced several publicly exposed details that could assist an attacker in planning further attack stages if the target were not authorized/in-scope:

| Observation | Tool | Risk Insight |
|-------------|------|---------------|
| Exact CMS & plugin versions exposed (WordPress 7.1.1, WP Download Manager 3.3.58) | whatweb | Version disclosure allows an attacker to cross-reference public CVE databases for known exploits against that specific version |
| Hosting provider & name servers revealed (HostGator) | whois | Confirms hosting environment, aiding social engineering or provider-specific attack planning |
| Server software & caching stack exposed via HTTP headers | curl -I | Reveals Apache + nginx-cache + WordPress REST API endpoint (`/wp-json/`), a common target for enumeration attacks |
| DNS infrastructure fully enumerated (SOA, MX, SPF, SRV records) | dnsrecon | Reveals mail server, DNS software version (BIND 9.16.23-RH), and cPanel autodiscover records — useful for email-based attack planning |
| Publicly indexed emails/sub-domains | theHarvester | Harvested addresses could be used as phishing targets; sub-domains may reveal forgotten or unmonitored services |

**Mitigating control identified:** The target is protected by **ModSecurity (SpiderLabs) WAF**, detected via `wafw00f`. This significantly raises the difficulty of any follow-on exploitation attempt, since malicious requests would likely be blocked or logged.

> **Note:** These are *reconnaissance-stage observations*, not confirmed vulnerabilities — no exploitation, unauthorized access, or intrusive testing was attempted. The purpose of this exercise was to demonstrate how much actionable intelligence can be gathered using only passive, publicly available information, which is precisely why minimizing this exposure is a core defensive practice.

---
## 🧩 Module 3: Active Network Scanning with Zenmap

**Environment:** Local LAN (WiFi adapter)

### Task 1–2 — Identify Local IP & Subnet
```cmd
ipconfig
```
![Local IP and Subnet](<local Ip and Subnet.png>)

**Finding:** Active LAN adapter (WiFi) — IP `192.168.100.12`, Subnet `255.255.255.0` → target range `192.168.100.0/24`.

### Task 3 — Ping Scan for Live Host

![Nmap 1](<Nmap 1.png>)

### Task 4 — Host Count
**Answer:** 5 hosts live in the subnet (including local PC).

### Task 5 — Live Host IP Addresses

| IP Address |
|------------|
| 192.168.100.1 |
| 192.168.100.11 |
| 192.168.100.12 |
| 192.168.100.51 |
| 192.168.100.66 |

### Task 6 — MAC Addresses

| IP Address | MAC Address | Vendor |
|------------|-------------|--------|
| 192.168.100.11 | E8:A6:60:B7:43:9B | Huawei Technologies |
| 192.168.100.51 | 72:8B:42:D3:3C:A6 | Unknown |
| 192.168.100.66 | 2E:70:58:AD:43:6E | Unknown |
| 192.168.100.12 | B6:2A:B9:1C:96:35 | Unknown |

### Task 7 — Network Topology Export
![Nmap 2](<Nmap 2.png>)

---

## 📋 Verification Checklist

- [x] WHOIS domain data captured
- [x] Web technology stack fingerprinted
- [x] DNS resolution confirmed
- [x] HTTP headers inspected
- [x] WAF presence detected
- [x] DNS records fully enumerated
- [x] Emails/sub-domains harvested via OSINT
- [x] Live hosts identified on local subnet
- [x] Network topology exported

---

## 🐞 Troubleshooting Log

| Issue | Cause | Resolution |
|-------|-------|-----------|
| `dnsrecon` returned "Could not resolve domain" | Domain entered without `.com` | Corrected to full FQDN: `dnsrecon -d networkwalks.com` — resolved successfully, 8 records found |
| `theHarvester` returned "unrecognized arguments" | Used `-1` instead of `-l` | Corrected flag: `-l` (lowercase L) sets the result limit |
| `theHarvester` returned "domain required" | Used `-b` in place of `-d` for target domain | `-d` specifies the target domain; `-b` specifies the source engine |
| Zenmap detected only 1–2 hosts initially | Scanned an isolated VMware virtual adapter (VMnet1) instead of the real LAN | Identified the adapter with an active Default Gateway (real router-connected network) before rescanning |

---

## 💡 What I Learned

This module made clear how much information is exposed publicly before an attacker ever touches a target system. Tools like `whois`, `whatweb`, and `dnsrecon` show that reconnaissance alone — without a single exploit attempt — can reveal a hosting provider, exact software versions, DNS infrastructure, mail server configuration, and firewall presence. This is precisely why **minimizing public exposure** is treated as a core defensive strategy in real security programs.

On the OSINT side, theHarvester demonstrated how easily emails and sub-domains tied to an organization can be aggregated from public sources — each one a potential entry point for phishing or credential attacks.

On the active scanning side, Zenmap showed how quickly live hosts, MAC addresses, and full network topology can be mapped from inside a network — reinforcing why network segmentation, DHCP monitoring, and anomaly detection on scan-like traffic matter in a defended environment.

---

## 🔗 Resources

- [Networkwalks Technologies](https://networkwalks.com)
- [Nmap Official Documentation](https://nmap.org/book/man.html)
- [theHarvester GitHub Repository](https://github.com/laramies/theHarvester)


---

### Author
**Syed Muhammad Ali Raza**
Cybersecurity & Ethical Hacking Intern — Networkwalks Technologies (Batch B083)
