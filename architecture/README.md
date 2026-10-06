# Lab Architecture

## Overview

The Home SOC Lab is built inside VirtualBox and includes several virtual machines used for monitoring, security testing and traffic analysis.

## Current Architecture

```text
Internet
   |
OPNsense
   |
--------------------------------
|              |               |
Ubuntu       Windows         Other VMs
Server         10
|
Wazuh
```
## Main Components
- OPNsense - firewall and network gateway
- Suricata - IDS/IPS
- Wazuh - SIEM/XDR
- Ubuntu Server - Wazuh host
- Windows 10 - monitored endpoint
- VirtualBox - virtualization platform
