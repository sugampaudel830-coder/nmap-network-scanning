# Internship Task: Basic Network Scanning with Nmap

##  Objective
The objective of this task is to perform a controlled network infrastructure scan against a target machine to identify active entry points, discover underlying service versions, map hostnames, and analyze potential security risks.

## 🛠️ Environment & Scope
* **Target IP Address:** `10.129.102.40`
* **Scan Parameters Utilized:** `nmap -sC -sV -Pn`
  * `-Pn`: Disables host discovery ping; treats the target as online to bypass potential firewall blocks.
  * `-sV`: Probes open ports to determine service and version information.
  * `-sC`: Runs default Nmap enumeration scripts against discovered open ports.

---

##  Identified Open Ports & Services

| Port / Protocol | State | Service | Software / Version | Security Significance & Analyst Findings |
| :--- | :--- | :--- | :--- | :--- |
| **22 / TCP** | Open | SSH | OpenSSH 7.4 (protocol 2.0) | Remote management interface. Version 7.4 is older and may contain known vulnerabilities. Security posture requires verifying that password authentication is disabled in favor of key-based pairs. |
| **80 / TCP** | Open | HTTP | Apache httpd 2.4.6 (CentOS) | Web server hosting an application. **Critical Finding:** The service issues a redirect to `http://connected.htb/`. Local host file configuration (`/etc/hosts`) is necessary for further application layer analysis. |
| **443 / TCP** | Open | HTTPS | Apache httpd 2.4.6 (CentOS) | Secure web traffic layer using OpenSSL 1.0.2k-fips. **SSL Certificate Common Name (CN):** `pbxconnect`. This strongly indicates the target server is hosting a PBX (Private Branch Exchange) VoIP/telephony control system. |

---

## Analytical Insights & Recommendations

1. **Operating System Footprint:** The service headers reveal `CentOS` and specific old package releases, indicating a legacy Linux environment.
2. **Domain Mapping Target:** To interact with the web interface running on ports 80/443, the domain `connected.htb` must be appended to the local attacking machine's hosts file:
```bash
   echo "10.129.102.40  connected.htb" | sudo tee -a /etc/hosts