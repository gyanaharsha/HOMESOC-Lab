# HomeSOC Enterprise Lab

A self-built Security Operations Centre simulation designed to develop hands-on experience equivalent to an entry-level SOC Analyst role. Built from scratch on an M2 MacBook Pro using cloud and virtualisation infrastructure — zero corporate budget, all real tools.

This repository documents everything: the architecture, the setup process, the mistakes, the fixes, and the security work. If you're reading this as a recruiter or fellow learner, every folder here represents actual work done, not theory.

---

## What This Project Is

I built this lab to bridge the gap between academic cybersecurity knowledge and real-world SOC operations. The goal is simple — by the time this project is complete, I will have hands-on experience with Active Directory, SIEM deployment, log analysis, threat detection, incident response, and vulnerability management. The same skills a Level 1 SOC Analyst uses every day.

The project runs across 24 weeks and is structured around what actually happens inside a corporate security operations centre — not CTF challenges, not guided tutorials with answers provided. Real configuration, real attacks, real detections, real documentation.

---

## Lab Architecture

```
┌─────────────────────────────────────────────────────┐
│                  HomeSOC Lab Network                 │
│                                                     │
│  ┌─────────────┐      ┌─────────────────────────┐   │
│  │  Kali Linux │      │   DC01 - Windows Server  │   │
│  │  (Attacker) │─────▶│   Active Directory DC    │   │
│  │  ARM64/UTM  │      │   lab.local domain       │   │
│  └─────────────┘      │   Azure (Free Tier)      │   │
│                       └─────────────────────────┘   │
│  ┌─────────────┐      ┌─────────────────────────┐   │
│  │Ubuntu Server│      │   Windows 11 Client      │   │
│  │ Wazuh SIEM  │      │   Domain-joined endpoint │   │
│  │  ARM64/UTM  │      │   ARM64/UTM              │   │
│  └─────────────┘      └─────────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

---

## Environment

| Component | Details |
|---|---|
| Host Machine | MacBook Pro M2 |
| Hypervisor | UTM 4.x (ARM64 virtualisation) |
| Attacker VM | Kali Linux ARM64 (UTM Gallery) |
| SIEM Host | Ubuntu Server 22.04 LTS ARM64 |
| Domain Controller | Windows Server 2022 (Microsoft Azure Free Tier) |
| Domain Client | Windows 11 ARM64 (UTM Gallery) |
| SIEM Platform | Wazuh (open source) |
| Domain | lab.local |

---

## Project Phases

| Phase | Topic | Status |
|---|---|---|
| Phase 1 | Linux & Lab Foundation | 🔄 In Progress |
| Phase 2 | Windows & Active Directory | 🔄 In Progress |
| Phase 3 | Networking & Packet Analysis | ⏳ Upcoming |
| Phase 4 | SIEM & Log Analysis | ⏳ Upcoming |
| Phase 5 | Attack Simulation & Threat Detection | ⏳ Upcoming |
| Phase 6 | Incident Response & Portfolio | ⏳ Upcoming |

---

## Repository Structure

```
HomeSOC-Lab/
│
├── README.md
├── ARCHITECTURE.md
│
├── infrastructure/
│   ├── network-diagram.png
│   ├── vm-setup-notes.md
│   └── active-directory/
│       ├── domain-setup.md
│       ├── ou-structure.md
│       └── user-list.md
│
├── log-infrastructure/
│   ├── wazuh-setup.md
│   ├── agent-configs/
│   └── log-sources.md
│
├── detection-rules/
│   ├── README.md
│   └── wazuh-custom-rules/
│
├── attack-simulations/
│   ├── mitre-attck-mapping.md
│   └── simulation-reports/
│
├── incident-response/
│   ├── IR-runbooks/
│   └── IR-reports/
│
├── vulnerability-management/
│   └── VA-reports/
│
├── threat-hunting/
│
├── scripts/
│   ├── python/
│   ├── bash/
│   └── powershell/
│
└── progress-log/
    └── day-001.md
```

---

## Progress Log

### Day 1 — Lab Infrastructure Setup
**Date:** June 6 2025

Today was entirely about getting the foundation right. Infrastructure work is unglamorous but it matters — a shaky lab environment creates problems in every phase that follows.

**What I set up:**

**Kali Linux (Attack Machine)**
- Deployed Kali Linux ARM64 via UTM Gallery on M2 MacBook Pro
- Hit ARM64 package compatibility issues immediately — several packages in the default Kali bundle (`crackmapexec`, `libboost-dev`, `python3-pyspnego`, `kali-linux-headless`) have no working ARM64 builds and caused dpkg to fail mid-upgrade
- Fixed by force-removing corrupted cached `.deb` files, purging the broken meta-packages, and running a clean apt cycle
- Verified all tools needed for the project are working: nmap, Wireshark, Hydra, Metasploit

```bash
nmap --version       # working
wireshark --version  # working
hydra -h             # working
msfconsole --version # working
```

**DC01 — Windows Server 2022 (Domain Controller)**
- Deployed Windows Server 2022 on Microsoft Azure Free Tier — chosen because native ARM64 Windows Server ISOs are not publicly available and emulating x86 in UTM was unstable
- Connected via RDP using Microsoft Remote Desktop (Mac)
- Installed Active Directory Domain Services role via Server Manager
- Promoted server to Domain Controller — domain: `lab.local`
- Created domain structure:

```
lab.local
├── IT          (3 users)
├── Finance     (4 users)
├── HR          (4 users)
└── Management  (4 users)
```

- Created 15 domain users with realistic Indian and international names across all OUs
- Disabled Windows Update service to preserve Azure free tier resources and prevent performance issues during lab sessions

**Ubuntu Server 22.04 (SIEM Host)**
- Deployed Ubuntu Server 22.04 LTS ARM64 via UTM
- Configured dual network interfaces: Shared Network (internet) and Emulated VLAN (lab network)
- Fixed missing `ping` utility — Ubuntu Server minimal install excludes it by default
- Verified internet connectivity via curl before running package updates
- Ran full system update — zero errors, clean ARM64 support unlike Kali
- Installed prerequisite packages: `net-tools`, `wget`, `gnupg2`

**Issues Encountered and Resolved**

| Issue | Cause | Fix |
|---|---|---|
| `brew: command not found` | Homebrew not installed | Installed Homebrew, added to PATH manually for M2 |
| Kali `NO_PUBKEY` GPG error | Outdated UTM Gallery image | Installed `kali-archive-keyring` with `--allow-unauthenticated` flag |
| `dpkg` broken pipe errors | Corrupted ARM64 `.deb` cache | Deleted cached files directly from `/var/cache/apt/archives/` |
| Windows Server ISO boot failure | SCSI interface incompatible with UTM emulation | Switched to Azure Free Tier instead of local emulation |
| Ubuntu `ping: command not found` | Minimal server install | `sudo apt-get install iputils-ping` |

**What's next:**
Installing Wazuh on Ubuntu Server — the SIEM that will ingest logs from every machine in the lab.

---

## Skills Being Developed

**Current phase:**
- Linux system administration
- Windows Server and Active Directory administration
- Virtualisation and lab environment management
- Cloud infrastructure basics (Azure)
- Troubleshooting ARM64 compatibility issues in security tooling

**Upcoming phases:**
- SIEM deployment and configuration (Wazuh)
- Network traffic analysis (Wireshark, tcpdump)
- Threat detection and custom rule writing
- MITRE ATT&CK framework mapping
- Incident response following NIST SP 800-61
- Vulnerability assessment (Greenbone/OpenVAS)

---

## Certifications (In Progress)

| Certification | Status | Target |
|---|---|---|
| Google Cybersecurity Certificate | 🔄 Course 3/8 | Month 1 |
| CompTIA Security+ SY0-701 | ⏳ Not started | Month 3 |
| Splunk Core Certified User | ⏳ Not started | Month 4 |
| CompTIA CySA+ | ⏳ Not started | Month 9 |
| Microsoft SC-200 | ⏳ Not started | Month 12 |

---

## Background

B.Tech in Computer Science (Amrita School of Engineering, 2022) and M.S. in Computer Science from Syracuse University — where I studied Cryptography, Cybersecurity, and Data Science. This project is the practical layer on top of that academic foundation. Textbook security knowledge only goes so far. This lab is where theory becomes muscle memory.

---

## Contact

- LinkedIn: https://www.linkedin.com/in/gyanaharsha/
- GitHub: https://github.com/gyanaharsha

---

*This lab is for educational purposes. All attack simulations are performed in an isolated environment against machines I own and operate.*
