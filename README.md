# Week 2 Penetration Testing Report: Footprinting & Network Scanning
| Field | Detail |
|---|---|
| Pentester | Hendrick Hipolity Martin |
| Program | Networkwalks Cybersecurity Internship (Batch B084, Intern ID NW-084-3JO) |
| Date | 24 November 2026 |
| Modules completed | W2-PM4 (theHarvester footprinting), W2-PM5 (Zenmap network scanning) |
| Targets | 1. microsoft.com (passive public-source lookup only, no direct contact)<br>2. My own local LAN (192.168.100.0/24) |
| Phases covered | Phase 1: Reconnaissance & Footprinting<br>Phase 2: Scanning & Network Discovery |

## Liability Disclaimer

These materials are for education and research purposes only. I performed these activities only on a network I own and on public information sources that can be read without contacting the target directly. Do not use anything here to break the law. Unauthorised access is a crime in most countries, even when nothing is damaged.

## Tools Used

| Tool | Purpose |
|---|---|
| Kali Linux (VirtualBox) | Footprinting environment |
| theHarvester 4.11.1 | Collect emails, hosts, IPs and ASNs from public sources |
| Zenmap (Nmap 7.993) | Find live hosts, IPs and MAC addresses; draw the topology |
| Windows CMD (`ipconfig`) | Identify local IP, subnet mask, gateway and MAC |

## 1. Footprinting with theHarvester (W2-PM4)

theHarvester is a passive reconnaissance tool: it reads public sources and never touches the target.

```bash
# Task 1: Baidu source, limit 1000
theHarvester -d microsoft.com -l 1000 -b baidu

# Task 2: all sources, limit 50
theHarvester -d microsoft.com -l 50 -b all
```

| Task | Result |
|---|---|
| Task 1 (Baidu) | No emails, hosts or IPs returned |
| Task 2 (all sources) | 4 emails, 147 IPs, 5 ASNs, 1 interesting URL, 9,531 host entries |

Many sources were skipped with "Missing API key" warnings, which is normal without paid keys. Results change between runs because public sources change what they return.

**Why it matters:** harvested emails are phishing targets, and every sub-domain widens the attack surface.

## 2. Network Scanning with Zenmap (W2-PM5)

```text
ipconfig
ipconfig /all
nmap -sn 192.168.100.0/24
```

- Local IP: `192.168.100.11`, mask `255.255.255.0`, gateway `192.168.100.1`
- Subnet: `192.168.100.0/24`
- Result: **5 live hosts** scanned in 4.43 seconds

| IP | MAC (partly hidden) | Note |
|---|---|---|
| 192.168.100.1 | E4:82:10:XX:XX:XX | Huawei router (gateway) |
| 192.168.100.11 | 48:A4:72:XX:XX:XX | My own PC |
| 192.168.100.17 | C4:91:0C:XX:XX:XX | Apple device |
| 192.168.100.91 | XX:XX:XX:XX:XX:XX | Unknown vendor (likely randomized MAC) |
| 192.168.100.120 | XX:XX:XX:XX:XX:XX | Unknown vendor (likely randomized MAC) |

The topology was saved as a PDF using Zenmap's **Topology → Legend → Save Graphic** feature.

## 3. Risk Analysis

| # | Finding | Evidence | Level |
|---|---|---|---|
| 1 | Very large public host list | 9,531 host entries returned | Medium |
| 2 | Internal-style host naming visible | Names with `corp`, `ppe`, `preprod` labels | Medium |
| 3 | IPs and ASNs discoverable | 147 IPs, 5 ASNs | Low |
| 4 | Public email addresses | 4 addresses found | Low |
| 5 | Multiple live hosts on local network | 5 hosts in 192.168.100.0/24 | Medium |
| 6 | Devices with unidentified MAC vendors | 2 unknown vendors | Low |

These are observations, not confirmed vulnerabilities. No exploitation or vulnerability validation was performed.

## 4. Recommendations

1. Regularly run OSINT tools against your own domains to see what an attacker sees.
2. Retire unused sub-domains and avoid publishing test or pre-production names.
3. Avoid revealing internal naming in host names.
4. Use email filtering and awareness training for publicly known mailboxes.
5. Monitor DNS and certificate transparency records.
6. Scan your own networks periodically and investigate unknown devices.
7. Keep network topology and device inventory documented.
8. Only test systems where authorization exists.

## 5. Key Takeaways

- Results depend heavily on which sources a footprinting tool uses.
- Even before any exploitation, public information and network responses reveal a lot.
- Good reports explain what was done, what was found, why it matters and how to reduce the risk.
- Reconnaissance and scanning must always stay within an authorized scope.

## Evidence

Screenshots are in the [`screenshots/`](screenshots) folder:

| File | Shows |
|---|---|
| `01_harvester_menu.png` | Opening theHarvester in Kali |
| `02_harvester_help.png` | `theHarvester -h` |
| `03_harvester_task1_baidu.png` | Task 1 result |
| `04_harvester_task2_start.png` | Task 2 command and warnings |
| `05_harvester_task2_asn_ips.png` | ASNs, URL and IPs found |
| `06_harvester_task2_emails_hosts.png` | Emails and hosts found |
| `07_ipconfig.png` | `ipconfig` output |
| `08_ipconfig_all_wifi.png` | `ipconfig /all`, Wi-Fi adapter |
| `09_zenmap_scan.png` | Zenmap ping scan |
| `10_zenmap_topology.png` | Zenmap topology |
| `11_zenmap_legend.png` | Topology with legend |
| `12_topology_pdf.png` | Saved topology PDF |

---

*Part of the Networkwalks Cybersecurity Internship, Week 2. For education purposes only.*
