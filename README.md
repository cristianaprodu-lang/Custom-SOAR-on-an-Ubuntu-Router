# Custom-SOAR-on-an-Ubuntu-Router
>This is my Bachelor's thesis. I'm making a custom SOAR (Security Orchestration, Automation, and Response) solution on an Ubuntu virtual machine in GNS3 acting as a router. 

Objectives:
-  **Architecture:** 3-zone GNS3 topology (LAN, DMZ, WAN) routed by an Ubuntu VM enforcing dynamic `nftables` rules.
-  **Detection (Suricata IDS):** Zero-latency, out-of-band L4-L7 deep packet inspection for real-time alerting.
-  **Custom SOAR (Python):** A from-scratch daemon that scores threats, validates them via Threat Intel APIs, and automates IP quarantine/unblocking.
-  **SOC Dashboard (Flask):** A web interface for live telemetry, incident history (SQLite), and manual quarantine control.
-  **Validation:** Benchmarking mitigation latency and system resource impact to prove legitimate traffic remains unaffected during attacks.
