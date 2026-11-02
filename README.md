# InfraScope Project : Network Infrastructure Audit and Mapping

<img src="Images/Schéma_réseau.png" width="60%" />

---

## 📌 Project Overview

This project focuses on auditing and assessing an existing enterprise IT infrastructure as part of an infrastructure security and administration project.

The objective was to analyze the current state of a company's IT infrastructure, identify potential security weaknesses, evaluate network reliability, and propose technical improvements to support future growth.

The audit was conducted on a fictional company named CloudNexa, with approximately 60 employees.

The infrastructure was analyzed using network discovery tools, configuration reviews, service enumeration, and documentation analysis.

### The project covers :

* Network infrastructure discovery and inventory
* Network topology analysis and mapping
* VLAN architecture and IP addressing
* Cisco switch configuration analysis
* Spanning Tree Protocol (STP)
* EtherChannel and link aggregation
* DHCP configuration and address management
* Server and service discovery
* Backup infrastructure assessment
* Firewall security assessment
* Virtualization infrastructure analysis
* Risk identification and criticality assessment
* Technical recommendations and remediation planning

---

## 🎯 Objectives

### The main objectives of this audit were to :

* Identify the network devices and servers currently present in the infrastructure.
* Compare the existing infrastructure with the documentation provided by the company.
* Analyze the network architecture and understand how the different VLANs communicate.
* Identify configuration issues affecting network performance and availability.
* Assess the security of critical services and infrastructure components.
* Evaluate the backup solution and virtualization environment.
* Identify single points of failure and potential availability risks.
* Build a risk matrix based on impact and likelihood.
* Propose technical recommendations to improve infrastructure security and reliability.
* Define a prioritized action plan for infrastructure modernization.

---

## 🏗️ Infrastructure Overview

The audit was performed on an existing enterprise network composed of several interconnected infrastructure components.

The environment includes :

* Cisco Catalyst switches
* A network edge router
* A FortiGate firewall
* A Proxmox virtualization server
* A NAS storage system
* A dedicated backup server
* Active Directory services
* PostgreSQL database services
* Web and mail relay services

The infrastructure is divided into several network zones to separate users, servers, administration, and DMZ services.

### Network Segmentation

The following networks were identified during the audit :

<img src="Images/Tableau_adressage.png" width="100%" />

The analysis revealed several differences between the actual infrastructure and the company's previous network documentation.

---

## 🔎 Part 1 : Network Discovery & Inventory

The first step consisted of identifying the devices currently connected to the different network segments.

Nmap was used to discover active hosts, identify open ports, and detect running services.

### 1.1 Host Discovery

Network discovery scans were performed on the authorized subnets :

```
nmap -sn 192.168.10.0/24
```
```
nmap -sn 192.168.20.0/24
```
```
nmap -sn 10.99.0.0/24
```
```
nmap -sn 172.16.0.0/24
```

This allowed me to identify active devices across the different network zones.

### 1.2 Port and Service Enumeration

Additional scans were performed to identify open ports and service versions.
```
nmap -sS -sV --top-ports 1000 -T4 -iL hotes_zones.txt
```

The results helped identify exposed services and outdated software components.

### 1.3 Infrastructure Inventory

The discovered devices were compared with the inventory provided by the company.

The audit identified :

* 8 compliant devices
* 5 modified devices
* 4 undocumented devices
* 3 devices missing from the actual infrastructure

A total of 19 infrastructure elements were inventoried.

This comparison highlighted several differences between the documented architecture and the actual environment.

---

## 🗺️ Part 2 : Network Architecture & Mapping

After identifying the infrastructure components, the next step was to reconstruct the logical network topology.

The network architecture was analyzed using Cisco configuration commands and device discovery information.

### 2.1 Network Topology

The infrastructure is organized around a central Cisco switch responsible for inter-VLAN routing and DHCP services.

The main components include :

* Internet access through an Orange fiber connection.
* An edge router connected to the firewall.
* A central Cisco Catalyst switch handling internal network traffic.
* Two access switches distributing connectivity across the floors.
* A Proxmox hypervisor hosting the virtual machines.
* A NAS and a dedicated backup server.
* Separate administration and DMZ network segments.

### 2.2 Network Configuration Analysis

Several Cisco IOS commands were used to understand the network architecture :
```
show version
```
```
show cdp neighbors
```
```
show lldp neighbors
```
```
show vlan brief
```
```
show interfaces trunk
```
```
show spanning-tree
```
```
show etherchannel summary
```
```
show ip interface brief
```

These commands helped identify VLAN configurations, network connections, spanning-tree behavior, and link aggregation settings.

### 2.3 Documentation Differences

One of the main findings was that the actual infrastructure no longer matched the documentation from March 2023.

For example :

* The documented two-switch Catalyst 9300 stack was reduced to a single core switch.
* The second Proxmox node was missing.
* The second Active Directory domain controller was no longer responding.
* The documented 4G Internet backup was not found.
* The planned second FortiGate firewall was absent.

This highlighted the importance of maintaining accurate infrastructure documentation.

---

## 🔀 Part 3 : Network Performance & Availability Assessment

The network architecture revealed several configuration problems affecting performance and availability.

### 3.1 Single Point of Failure

The entire internal network relies on a single core switch.

This device is also connected outside the UPS protection system.

A power outage in 2025 had already caused a complete network interruption.

This represents a significant availability risk for the company.

### 3.2 Spanning Tree Protocol

The analysis of the Spanning Tree Protocol configuration revealed that SW-ACC2 was acting as the root bridge.

This resulted in an unexpected blocking state on one of the network links.

The traffic from the different floors was consequently using a single 1 Gb/s link, contributing to the network performance issues reported by users.

The recommended correction is to configure SW-CORE as the primary STP root bridge and restore the appropriate EtherChannel configuration.

### 3.3 EtherChannel Analysis

The EtherChannel configuration was also examined using :
```
show etherchannel summary
```
The results revealed an inconsistent link aggregation configuration.

One of the interfaces was not correctly participating in the channel, requiring configuration adjustments.

These findings demonstrate how incorrect Layer 2 configurations can affect network performance and redundancy.

---

## 🖥️ Part 4 : Server & Backup Infrastructure Assessment

The server infrastructure was examined to identify outdated systems, exposed services, and potential risks affecting data availability.

### 4.1 Backup Server Analysis

The audit identified a backup server running Debian 7, an obsolete operating system.

Several services were exposed :

* Port
* Service
* Identified version
* 21
* FTP
* ProFTPD 1.3.5
* 22
* SSH
* OpenSSH 6.0p1
* 23
* Telnet
* Linux telnetd
* 3306
* MySQL
* 5.5.62

The presence of outdated services and insecure protocols such as Telnet represents a significant security concern.

The recommended actions include :

* Disabling Telnet and FTP.
* Restricting access to the backup server.
* Replacing the obsolete server with a supported backup solution.
* Testing backup restoration procedures.

### 4.2 Virtualization Infrastructure

The virtualization environment was also assessed.

The audit identified a single Proxmox hypervisor, PVE01, hosting the virtual machines.

The second node documented in the previous infrastructure inventory was missing.

This creates a single point of failure for the virtualized environment.

A second Proxmox node and a high-availability architecture were recommended as part of the long-term improvements.

4.3 Database Services

The PostgreSQL environment was identified as requiring modernization.

A migration to a supported PostgreSQL version was therefore included in the recommendations.

---

## 🛡️ Part 5 : Firewall & Security Assessment

The perimeter security infrastructure was examined to evaluate the status of the firewall and its protection services.

### 5.1 FortiGate Assessment

The audit revealed that the FortiGuard security subscriptions associated with the firewall had expired at the end of November 2023.

The security definitions were outdated, with the last recorded update dating from 2015.

This represents an important weakness in the perimeter security strategy.

### 5.2 Recommended Improvements

The following actions were recommended :

* Renew the FortiGuard subscriptions.
* Update the security signatures.
* Review the firewall security configuration.
* Plan the deployment of a second firewall.
* Improve perimeter redundancy.

The objective is to restore an up-to-date security protection layer and reduce the risks associated with the failure of a single firewall.

---

## 📊 Part 6 : Risk Assessment & Criticality Matrix

After analyzing the different infrastructure components, the identified issues were classified according to their impact and likelihood.

The audit uses a scoring method based on :

Risk Score = Impact × Likelihood

Each parameter is evaluated on a scale from 1 to 4.

The resulting scores are classified as follows :

tableau de criticité

The final assessment identified :

* 5 critical findings
* 3 high findings
* 1 moderate finding

This approach made it possible to prioritize the remediation actions according to their potential impact on the company's infrastructure.

---

## 🔧 Part 7 : Recommendations & Remediation Plan

The final stage of the project consisted of translating the audit findings into concrete technical recommendations.

The proposed actions were divided into three implementation periods.

### Immediate Actions (Less than 1 Month)

The first priority is to address the most urgent infrastructure weaknesses.

Recommended actions :

* Connect SW-CORE to the UPS.
* Correct the Spanning Tree root bridge configuration.
* Restore the EtherChannel configuration.
* Isolate and secure SRV-BKP01.
* Renew the FortiGuard subscriptions.
* Free up unused DHCP addresses.

### Medium-Term Actions (Within 3 Months)

The second phase focuses on improving the infrastructure's capacity and reliability.

Recommended actions :

* Expand the user network address space from /24 to /23, or introduce network segmentation by floor.
* Replace the obsolete backup server.
* Test backup restoration procedures.
* Migrate PostgreSQL to a supported version.
* Install an additional access switch on the first floor.
* Restore a second Active Directory domain controller.
* Update the infrastructure documentation.

### Long-Term Actions (Within 12 Months)

The final phase focuses on infrastructure modernization and redundancy.

Recommended actions :

* Add a second Catalyst 9300 to the core stack.
* Implement a second power supply.
* Deploy a second Proxmox node.
* Configure a high-availability virtualization cluster.
* Plan the deployment of redundant firewall and Internet connectivity solutions.
* Introduce infrastructure monitoring.

The objective is to build a more reliable infrastructure capable of supporting the company's planned recruitment of 15 additional employees in early 2027.

---

## 🧰 Technologies & Tools

Technology / Tool

Purpose

Nmap

Network discovery and port scanning

Cisco IOS

Switch configuration analysis

SSH

Remote configuration inspection

SNMP

Network device information collection

DNS

Network name resolution analysis

SMB / smbclient

File-sharing service assessment

PostgreSQL / psql

Database service inspection

OpenSSL

Security and certificate inspection

curl

HTTP/HTTPS service inspection

Proxmox VE

Virtualization infrastructure

FortiGate

Firewall and perimeter security

draw.io

Network topology documentation

---

## 📚 Skills Demonstrated

Through this project, I developed and practiced several technical skills related to infrastructure auditing, network administration, and cybersecurity.

Networking

Network discovery and enumeration

Network topology reconstruction

VLAN architecture analysis

IP addressing and subnetting

DHCP configuration analysis

Spanning Tree Protocol (STP)

EtherChannel and LACP

Cisco IOS command-line inspection

Network performance troubleshooting

System Administration

Linux server assessment

Service enumeration

SSH administration

DNS analysis

SMB service inspection

PostgreSQL assessment

Proxmox virtualization analysis

Backup infrastructure assessment

Cybersecurity

Infrastructure security auditing

Identification of outdated services

Detection of insecure protocols

Firewall security assessment

Risk identification and classification

Single point of failure analysis

Security recommendations

Infrastructure resilience assessment

Documentation & Methodology

Technical inventory management

Network architecture documentation

Audit report writing

Risk matrix creation

Technical remediation planning

Infrastructure documentation comparison

---

## 📁 Repository Structure

```text
.
├── README.md
└── Images/
    ├── Plan_adressage.png
    ├── Schéma_réseau.png
    ├── Tableau_constats.png
    └── Tableau_criticité.png
```

The documentation directory contains the audit report and the infrastructure inventory used during the assessment.

---

## ✅ Results

The audit successfully provided an overview of the company's existing IT infrastructure and identified several areas requiring improvement.

The main results include :

* 19 infrastructure elements inventoried.
* Differences identified between the actual infrastructure and the 2023 documentation.
* Network topology and IP addressing reconstructed.
* Critical network performance and availability issues identified.
* Outdated server services and security weaknesses detected.
* Firewall subscription expiration identified.
* Backup and virtualization weaknesses assessed.
* 9 findings classified according to their criticality.
* A prioritized remediation plan established for immediate, medium-term, and long-term improvements.

The audit provides a technical foundation for improving the security, reliability, and scalability of the CloudNexa infrastructure.

---

## 🎓 Context

Academic project : Bachelor 3 Cybersecurity

This project was completed during the 2026–2027 academic year as part of an infrastructure auditing and cybersecurity assignment.

It allowed me to apply practical network discovery, infrastructure analysis, system administration, and risk assessment techniques in an enterprise environment.

---

## 👤 Author

Sonia LOPEZ

Bachelor's Degree : Cybersecurity

---

## 📌 Key Takeaways

This project helped me understand that securing an enterprise infrastructure involves much more than configuring firewalls or protecting individual servers.

It requires a global understanding of how network devices, servers, services, backups, and security mechanisms interact.

Through this audit, I was able to identify weaknesses, understand their potential impact on business operations, and propose realistic improvements based on technical priorities.
