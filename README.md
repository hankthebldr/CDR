# XSIAM-Prisma Cloud CDR Lab Environment

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

This repository provides a hands-on lab environment for practicing **Cloud Detection and Response (CDR)** using **Palo Alto Networks Cortex XSIAM** and **Prisma Cloud**. The labs simulate real-world, cloud-native threats within **Kubernetes** environments, focusing on container security challenges.
By using this lab, security practitioners can gain practical experience in detecting and responding to threats like containerized cryptominers, vulnerable deployments, and behavioral anomalies (ABIOC) using advanced cloud security tools.

**Target Audience:** Cloud Security Engineers, SOC Analysts, Security Researchers, and anyone interested in hands-on experience with XSIAM and Prisma Cloud for container security.

## Setup & Scope 
the CDR attack simulation is **Cloud Agnostic** demonstration of container and kubernetes security. This simulation can be deployed in any derrative of kuberentes including implementations like microk8s, k3s AKS, GKE, EKS, Openshift etc. The current file CDR.yml will execute different simulations automatically. THe script is built with the native heurisitic and behavioral detections that we're able to use to generate sensor and analysitcs data. Its best practice to run the script for 1hr, and the tear it down. 

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
    Optional (wget)
    ``` bash
    wget https://raw.githubusercontent.com/hankthebldr/CDR/refs/heads/master/cdr.yml
    ```
3.  **Deploy Lab Environment:**
    * *(Instructions needed here - e.g., Apply Kubernetes manifests?)*
    * Example: `kubectl apply -f <your-deployment-manifest.yaml>`

    ** Simulation will begin automatically however, you may want to start/stop or preform operations on the objects within the detectino container **
      
6.    **Access the Simulation Container Trouble Shooting or Manual Script Execution :**
    * Identify the pod running the simulation environment (e.g., `alpine-cdr-1`).
    * Get a shell into the container:
        ```bash
        kubectl exec -it <your-pod-name> -- /bin/sh # Or /bin/bash if available
        ```
7.  ** Manually Executing the Simulation Script:**
    * Navigate to the script location within the container (if applicable).
    * Execute the simulation script, the default entrypoint within the container will pull and create the virush.sh, there are additional script runners that are called by the kubernetes manifest that will created detection scenarios 
        ```bash
        ./virus.sh; ./loader.sh; 
        ```
    * *(Add specific script name and execution details here)*

8.  **Observe in XSIAM/Prisma Cloud:**
    * Monitor Prisma Cloud Compute (Incidents > Runtime Events / Monitor > Runtime > Container Observations) for policy violations and alerts.
    * Monitor Cortex XSIAM for incoming alerts, incidents, and related telemetry from Prisma Cloud. Analyze the events using XSIAM's investigation tools.
  
9. **Observer in XDR**
   * XDR will have created an Incident and should be avial
  
10. **Teardown**
    ```bash
    kubectl delete -f cdr.yml
    ```
## Lab Scenarios & Simulation Details

The core of the detection scenario is premised within a cloud native/container orchestrated enviorment. When appying the deployment file the orchestrated cluster will automatically call all of the required scripts to provide agent and analytics detections. 

---
### **Simulation Script Breakdown:**

#### **1. Initial Setup**
* **System Preparation**: Updates Alpine packages (`apk update && apk upgrade`).
* **Tool Installation**: Installs `nmap`, `tor`, `socat`, `bash`, `python3`, `busybox-extras`, `gcc`, `clang`, `cmake`, `openssl-dev`.

#### **2. Simulated Scenarios**

* **Scenario 1: Enumeration and Reconnaissance (MITRE T1082, T1016)**
    * Downloads and run smart linux enumeration scritps that will investigate the different container filesystem and directories for potential vulnerabililies
    * pulls upstream scripts that creates malicous objects, it will also preform container specific enumerations and tests
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

* Understand how XSIAM, XDR, and Cortex Cloud
* Identify common attacker TTPs targeting cloud-native infrastructure.
* Analyze runtime security events and alerts from Prisma Cloud within XSIAM.
* Practice incident response workflows for cloud-based threats.
* Gain familiarity with container security concepts and potential vulnerabilities.

## Contributing

* Please create forks, create issues or issue pull requrests to contribute. This repository is maintained by the DC community or myself and has no long term maintain er

## References

* [XSIAM Documentation: Ingest alerts and assets from Prisma Cloud](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-Documentation/Ingest-alerts-and-assets-from-Prisma-Cloud)
* [Unit42 Research: Large-Scale Monero Cryptomining Operation](https://unit42.paloaltonetworks.com/unit42-large-scale-monero-cryptocurrency-mining-operation-using-xmrig/)
* [Palo Alto Networks: Test a Sample Malware File for WildFire](https://docs.paloaltonetworks.com/advanced-wildfire/administration/configure-advanced-wildfire-analysis/verify-wildfire-submissions/test-a-sample-malware-file)
