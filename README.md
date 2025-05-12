# XSIAM-Prisma Cloud CDR Lab Environment

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) ## Overview

This repository provides a hands-on lab environment for practicing **Cloud Detection and Response (CDR)** using **Palo Alto Networks Cortex XSIAM** and **Prisma Cloud**. The labs simulate real-world, cloud-native threats within **Kubernetes** environments, focusing on container security challenges.
By using this lab, security practitioners can gain practical experience in detecting and responding to threats like containerized cryptominers, vulnerable deployments, and behavioral anomalies (ABIOC) using advanced cloud security tools.

**Target Audience:** Cloud Security Engineers, SOC Analysts, Security Researchers, and anyone interested in hands-on experience with XSIAM and Prisma Cloud for container security.
## Setup & Scope 
the CDR attack simulation is **Cloud Agnostic** demonstration of container and kubernetes security. This simulation can be deployed in any derrative of kuberentes including implementations like microk8s, k3s AKS, GKE, EKS, Openshift etc. 

For 

## Features

* **Realistic Threat Simulation:** Executes a script simulating various attacker tactics and techniques within a container.
* **MITRE ATT&CK Alignment:** Demonstrates techniques covering reconnaissance, initial access, execution, persistence, privilege escalation, defense evasion, credential access, discovery, lateral movement, collection, and exfiltration.
* **Container-Specific Scenarios:** Includes tests for container enumeration, escape vulnerabilities (e.g., privileged containers), and cryptomining malware (XMRig).
* **Palo Alto Networks Integration:** Designed to generate telemetry and alerts viewable in XSIAM and Prisma Cloud.
* **WildFire Integration:** Includes steps to trigger and test WildFire malware analysis submissions.
* **Network Scanning Simulation:** Simulates internal and external network discovery.

## Prerequisites

* Access to a **Cortex XSIAM** instance.
* Access to a **Prisma Cloud Compute** instance (SaaS or self-hosted).
* A **Kubernetes cluster** where you can deploy workloads (e.g., Minikube, Kind, GKE, EKS, AKS).
* `kubectl` configured to interact with your cluster.
* Prisma Cloud Defender deployed to your Kubernetes cluster and connected to Prisma Cloud Compute.
* XSIAM configured to ingest alerts and data from your Prisma Cloud Compute instance. (See: [Ingest alerts and assets from Prisma Cloud](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-Documentation/Ingest-alerts-and-assets-from-Prisma-Cloud))
* Basic understanding of Kubernetes, Docker/containers, and Linux command line.

## Getting Started

1.  **Clone the Repository:**
    ```bash
    git clone [https://github.com/hankthebldr/CDR.git](https://github.com/hankthebldr/CDR.git)
    cd CDR
    ```
2.  **Deploy Lab Environment:**
    * *(Instructions needed here - e.g., Apply Kubernetes manifests?)*
    * Example: `kubectl apply -f <your-deployment-manifest.yaml>`
3.  **Access the Simulation Container:**
    * Identify the pod running the simulation environment (e.g., `alpine-cdr-1`).
    * Get a shell into the container:
        ```bash
        kubectl exec -it <your-pod-name> -- /bin/sh # Or /bin/bash if available
        ```
4.  **Run the Simulation Script:**
    * Navigate to the script location within the container (if applicable).
    * Execute the simulation script:
        ```bash
        ./<your-simulation-script-name>.sh
        ```
    * *(Add specific script name and execution details here)*

5.  **Observe in XSIAM/Prisma Cloud:**
    * Monitor Prisma Cloud Compute (Incidents > Runtime Events / Monitor > Runtime > Container Observations) for policy violations and alerts.
    * Monitor Cortex XSIAM for incoming alerts, incidents, and related telemetry from Prisma Cloud. Analyze the events using XSIAM's investigation tools.

## Lab Scenarios & Simulation Details

The core of this lab involves executing a script within a container (based on Alpine/BusyBox) that simulates a multi-stage attack. The script performs actions designed to trigger detections in Prisma Cloud and XSIAM.

*(You can keep the detailed breakdown from your original README here, perhaps refining the formatting slightly)*

---

### **Simulation Script Breakdown:**

#### **1. Initial Setup**
* **System Preparation**: Updates Alpine packages (`apk update && apk upgrade`).
* **Tool Installation**: Installs `nmap`, `tor`, `socat`, `bash`, `python3`, `busybox-extras`, `gcc`, `clang`, `cmake`, `openssl-dev`.

#### **2. Simulated Scenarios**

* **Scenario 1: Enumeration and Reconnaissance (MITRE T1082, T1016)**
    * Downloads and runs LinEnum/Linpeas scripts.
* **Scenario 2: Malware Simulation (MITRE T1486, T1105)**
    * Downloads/executes simulated Unix backdoors and Conti ransomware samples.
    * Executes Palo Alto Networks WildFire test files.
    * Creates staged malware triggers.
* **Scenario 3: MITRE ATT&CK TTP Demonstrations**
    * **Initial Access/Execution:** SSH attempt (T1078), Reverse Shell (Bash/Netcat) (T1059.004).
    * **Persistence:** Cron jobs, `.profile` modification (T1053.005).
    * **Privilege Escalation:** `sudoers` modification (T1548.002).
    * **Defense Evasion:** Log deletion (`/var/log`), file permission changes (T1070.001, T1070.004).
    * **Credential Access:** Dumps `/etc/shadow`, extracts `.bash_history` (T1003).
    * **Discovery:** System/network info gathering, process/directory listing (T1083, T1049).
    * **Lateral Movement:** SSH attempt (T1021.001).
    * **Collection/Exfiltration:** `tar` and `nc` usage (T1560, T1041).
* **Scenario 4: Container Enumeration and Exploitation (MITRE T1611)**
    * Downloads and runs DEEPCE container enumeration scripts.
    * Detonates a specific cryptominer (XMRig variant) based on Unit42 research.
* **Scenario 5: Network Scanning (MITRE T1046)**
    * Performs `nmap` scans (local/remote). Allows manual execution of specific scans.
* **Scenario 6: Execution of Malware (Continued - MITRE T1105)**
    * Executes downloaded backdoors, C2 clients, etc.

#### **3. Detection Opportunities**

This simulation should trigger detections based on:

* **Network Activity**: Reverse shells, C2 traffic, TOR usage, Nmap scans.
* **File System Changes**: Malicious file downloads (e.g., `/bin`, `/loader.sh`), sensitive file modification (`/etc/sudoers`, `/etc/shadow`).
* **Process Behavior**: Execution of recon scripts, malware binaries, privilege escalation commands, cryptomining processes.
* **Container Runtime Anomalies**: Privilege escalation attempts, container escape tools (DEEPCE), unusual process activity within the container.
* **Persistence**: Cron job creation, profile script modification.
* **Exfiltration**: Use of tools like `tar` combined with `nc`.
* **Log Tampering**: Deletion of files in `/var/log`.

---

## Learning Objectives

* Understand how XSIAM and Prisma Cloud detect threats in containerized environments.
* Identify common attacker TTPs targeting cloud-native infrastructure.
* Analyze runtime security events and alerts from Prisma Cloud within XSIAM.
* Practice incident response workflows for cloud-based threats.
* Gain familiarity with container security concepts and potential vulnerabilities.

## Contributing

*(Optional: Add guidelines here if you welcome contributions. e.g., "Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.")*

## License

*(Optional: Specify the license for your project. If you added the badge, make sure it matches.)*
* Example: This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.*

## References

* [XSIAM Documentation: Ingest alerts and assets from Prisma Cloud](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-Documentation/Ingest-alerts-and-assets-from-Prisma-Cloud)
* [Unit42 Research: Large-Scale Monero Cryptomining Operation](https://unit42.paloaltonetworks.com/unit42-large-scale-monero-cryptocurrency-mining-operation-using-xmrig/)
* [Palo Alto Networks: Test a Sample Malware File for WildFire](https://docs.paloaltonetworks.com/advanced-wildfire/administration/configure-advanced-wildfire-analysis/verify-wildfire-submissions/test-a-sample-malware-file)
