# CDR
# XSIAM-Prisma CDR Lab

## Overview

This repository serves as a comprehensive lab environment for practicing Cloud Detection and Response (CDR) using **XSIAM** and **Prisma Cloud**. The labs are designed to simulate real-world cloud-native threats, leveraging **Kubernetes**, container orchestration platforms, and **Palo Alto Networks** technologies. These labs provide a hands-on approach to explore advanced detection techniques and response scenarios against cloud workloads, enabling users to master the application of cloud security tools and methodologies in a controlled environment.

Key scenarios include detection of containerized cryptominers, identifying vulnerable deployments, analyzing behavioral anomalies using ABIOC (Analytics Behavior Indicator of Compromise) techniques, and integrating **WildFire** threat intelligence for enhanced detection capabilities. By utilizing these labs, practitioners will gain practical experience with critical cloud security components that are essential for a robust security posture.


### **Summarization and Analysis of the Script for Container-Specific Detection**

This script is a comprehensive simulation of malicious activity designed to test detection and response capabilities in a containerized environment. It encompasses multiple MITRE ATT&CK tactics and techniques while leveraging the capabilities of a lightweight container like BusyBox/Alpine. Below is a breakdown of the script activities:

---

### **1. Initial Setup**
- **System Preparation**: Updates and upgrades the Alpine package manager.
- **Tool Installation**: Installs various tools to enable scanning, enumeration, and exploitation:
  - Network tools: `nmap`, `tor`, `socat`
  - Script execution environments: `bash`, `python3`, `busybox-extras`
  - Build tools: `gcc`, `clang`, `cmake`, `openssl-dev`

---

### **2. Simulated Scenarios**
#### **Scenario 1: Enumeration and Reconnaissance**
- **LinEnum and Linpeas Execution**: Downloads and runs enumeration scripts to collect Linux system and privilege information.
- **Objective**: Simulate discovery and data collection activities (T1082, T1016).

---

#### **Scenario 2: Malware Simulation**
- **Unix Backdoor and Conti Malware**:
  - Downloads, executes, and replicates backdoor binaries in suspicious directories (`/bin`, `/loader.sh`).
  - Simulates ransomware behavior with Conti and Unix backdoors (T1486).
- **Objective**: Test detection for malicious file downloads, privilege escalations, and persistence mechanisms.

---

#### **Scenario 3: MITRE ATT&CK TTP Demonstrations**
- **Initial Access and Execution**:
  - Attempts SSH access (T1078).
  - Creates a reverse shell using Bash and Netcat (T1059.004).
- **Persistence**:
  - Adds cron jobs and modifies `.profile` for backdoor access (T1053.005).
- **Privilege Escalation**:
  - Modifies `sudoers` and switches to root (T1548.002).
- **Defense Evasion**:
  - Deletes logs and restricts access to `/etc/shadow` (T1070.001, T1070.004).
- **Credential Access**:
  - Dumps `/etc/shadow` and extracts passwords from `.bash_history` (T1003).
- **Discovery**:
  - Gathers system info, network interfaces, processes, and directory listings (T1083, T1049).
- **Lateral Movement**:
  - Attempts SSH to a target machine (T1021.001).
- **Data Collection and Exfiltration**:
  - Archives and exfiltrates data using tar and Netcat (T1560, T1041).

---

#### **Scenario 4: Container Enumeration and Exploitation**
- **DEEPCE Usage**:
  - Downloads and runs container enumeration scripts (DEEPCE) to simulate privilege escalation (T1611).
- **Objective**: Test detection of container-specific exploits like privileged mode abuse.

---

#### **Scenario 5: Network Scanning**
- **Local and Remote Scans**:
  - Performs network scanning using Nmap for local and external addresses (T1046).
- **Objective**: Simulate network discovery activities for detecting reconnaissance.

---

#### **Scenario 6: Execution of Malware**
- **Backdoors, C2 Clients, and Malware Execution**:
  - Executes malicious binaries and scripts such as Conti ransomware and Unix backdoor (T1105).
- **Error Handling**: Suppresses errors during execution for resilience against detection failures.

---

### **Detection and Behavioral Triggers**
1. **Network Activity**:
   - Unusual outbound connections (e.g., reverse shells, C2 traffic).
   - Network scanning and reconnaissance.
2. **File System Changes**:
   - Creation of suspicious files and binaries in `/bin` or `/loader.sh`.
   - Modifications to `/etc/sudoers` and `/etc/shadow`.
3. **Process Behavior**:
   - Execution of enumeration scripts and malware binaries.
   - Privileged operations via `sudo` or capabilities.
4. **Persistence Indicators**:
   - Newly added cron jobs.
   - Modifications to `.profile` for backdoor access.
5. **Exfiltration**:
   - Use of `tar`, `nc`, and direct outbound transfers.

---

### **Opportunities for Detection in Containers**
- **Container Escape**: Monitoring for attempts to exploit privileged containers (`DEEPCE`, privileged shell operations).
- **File Access Patterns**: Detect unusual reads/writes to sensitive files like `/etc/shadow`.
- **Network Anomalies**: Alert on scanning, reverse shell attempts, or unauthorized outbound connections.
- **Malware Artifacts**: Scan for known malicious binaries or hashes.
- **Log Tampering**: Detect deletion of log files in `/var/log`.

---

This script offers a robust way to simulate malicious activities and evaluate the effectiveness of your container-specific detection tools. It covers multiple attack vectors and provides opportunities to validate response mechanisms against real-world threats.


## Reference Links
https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-Documentation/Ingest-alerts-and-assets-from-Prisma-Cloud