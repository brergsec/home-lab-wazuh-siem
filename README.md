# 🛡️ Home Lab — Wazuh SIEM Setup

> A fully operational SIEM environment built with Wazuh to practice threat detection, log analysis, and incident monitoring.

![Wazuh](https://img.shields.io/badge/Wazuh-4.7.5-blue?style=flat-square&logo=wazuh)
![Ubuntu](https://img.shields.io/badge/Ubuntu_Server-26.04_LTS-orange?style=flat-square&logo=ubuntu)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.2.8-183A61?style=flat-square&logo=virtualbox)
![Debian](https://img.shields.io/badge/Host-Debian_13_(trixie)-A81D33?style=flat-square&logo=debian)
![Status](https://img.shields.io/badge/Status-Operational-brightgreen?style=flat-square)

---

## 📋 Objective

Build an isolated **Security Information and Event Management (SIEM)** environment inside a virtual machine to develop hands-on skills in:

- Threat detection and alert analysis
- Log and security event monitoring
- Linux environment hardening
- Operating the full Wazuh stack (Manager + Indexer + Dashboard + Agent)

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────┐
│           HOST MACHINE (Debian 13)          │
│                                             │
│   ┌─────────────────┐                       │
│   │   Wazuh Agent   │  ──── events ──────►  │
│   └─────────────────┘                       │
│                                             │
│   ┌─────────────────────────────────────┐   │
│   │    VirtualBox (Wazuh-Ubuntu-Server) │   │
│   │                                     │   │
│   │  ┌───────────┐  ┌───────────────┐   │   │
│   │  │  Manager  │  │    Indexer    │   │   │
│   │  └───────────┘  └───────────────┘   │   │
│   │         ┌──────────────┐            │   │
│   │         │  Dashboard   │ :443       │   │
│   │         └──────────────┘            │   │
│   └─────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

---

## 🧰 Stack

| Component | Version | Role |
|---|---|---|
| Wazuh Manager | 4.7.5 | Processes and analyzes security events |
| Wazuh Indexer | 4.7.5 | Stores and indexes alerts |
| Wazuh Dashboard | 4.7.5 | Web-based visualization interface |
| Wazuh Agent | 4.7.5 | Collects events from the host (Debian) |
| VirtualBox | 7.2.8 | Hypervisor |
| Ubuntu Server | 26.04 LTS | VM OS (Manager) |
| Debian | 13 (trixie) | Host OS (Agent) |

**VM Specs:** 4096MB RAM · 4 CPUs · 50GB Disk

---

## 📦 Setup Steps

### Step 1 — VirtualBox Installation
Installed VirtualBox 7.2.8 via `.deb` package on Debian 13.  
Issues resolved: `vboxdrv` kernel module failure, missing Qt6 shared library, broken dependencies.

### Step 2 — Virtual Machine Creation
Configured `Wazuh-Ubuntu-Server` VM with Ubuntu Server 26.04 LTS.  
Issue resolved: VT-x disabled in BIOS; KVM modules conflicting with VirtualBox.

### Step 3 — Wazuh Installation
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
```
All-in-one installation: Manager + Indexer + Dashboard.

### Step 4 — Agent Deployment
Wazuh Agent installed on the Debian host machine, pointed at the VM's Host-Only IP.  
Dashboard confirmed showing 1 active agent reporting security events.

📄 **Full step-by-step documentation:** [here](docs/setup-guide.md)

---

## 🔒 Hardening Measures

| Measure | Description |
|---|---|
| **Credential rotation** | All default passwords rotated using `wazuh-passwords-tool.sh -a` |
| **UFW Firewall** | Only ports 443, 1514, 1515, and 22 allowed; all others denied by default |
| **Internal port isolation** | Ports 9200 (Indexer) and 55000 (API) accessible locally only |
| **Repository lock** | Wazuh apt repository disabled post-install to prevent unintended upgrades |
| **VM Snapshot** | Snapshot taken after full hardening for instant rollback capability |

---

## 🐛 Key Issues & Solutions

| Issue | Solution |
|---|---|
| `vboxdrv` failed to load | `apt install linux-headers-$(uname -r)` + `vboxconfig` |
| Broken Qt6 dependencies | `apt --fix-broken install` |
| VT-x disabled in BIOS | Enabled in BIOS settings + unloaded conflicting KVM kernel modules |
| Agent pointing to wrong Manager IP | Updated `<address>` in `/var/ossec/etc/ossec.conf` |
| SSL warning on Dashboard | Self-signed certificate — expected behavior in a lab environment |

---

## 🗺️ Next Steps

⚠️ Pending: Make KVM blacklist persistent (/etc/modprobe.d/blacklist-kvm.conf) to avoid running modprobe -r manually after every reboot.

- [ ] Simulate attack scenarios and observe detections in the Dashboard
- [ ] Write custom Wazuh detection rules
- [ ] Explore MITRE ATT&CK mapping within Wazuh
- [ ] Make KVM module removal persistent via `/etc/modprobe.d/blacklist.conf`

---

## 👤 Author : Brenno R.G.

Built as a homelab project to develop practical Blue Team / SOC skills.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat-square&logo=linkedin)](https://linkedin.com/in/bren-r-g-a14a09401)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github)](https://github.com/brergsec)
