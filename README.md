# Home SOC Lab

Home SOC Lab built for learning security monitoring, network analysis, intrusion detection and incident investigation using Wazuh, OPNsense, Suricata and Sysmon.

## Overview

This project documents the development of my personal Security Operations Center (SOC) lab.

The environment is designed to practice:

- security monitoring
- network traffic analysis
- intrusion detection
- log analysis
- endpoint monitoring
- incident investigation
- firewall configuration
- SIEM administration
- custom detection engineering
- troubleshooting

## Technologies

The lab currently uses:

- VirtualBox
- Ubuntu Server
- Windows 10 / Windows test VM
- Wazuh
- Sysmon
- OPNsense
- Suricata
- rsyslog
- Wireshark
- tcpdump
- Nmap

## Project Goals

The main goal of this project is to gain practical experience with tools and workflows commonly used by SOC analysts.

The environment is used to:

- collect security telemetry
- monitor endpoint and network activity
- detect suspicious traffic
- create custom IDS and SIEM rules
- generate controlled test traffic
- investigate alerts
- document detection scenarios
- prepare the lab for future Red Team and Purple Team exercises

## Documentation

Detailed documentation for each component:

- [Lab Architecture](architecture/README.md)
- [OPNsense](opnsense/README.md)
- [Suricata](suricata/README.md)
- [Wazuh](wazuh/README.md)

## Current Lab Architecture

The current lab includes:

- OPNsense as firewall, gateway and network segmentation point
- Suricata IDS running on OPNsense
- Ubuntu Server running Wazuh Manager and rsyslog
- Windows endpoint monitored with Wazuh Agent and Sysmon
- Wireshark and tcpdump for packet analysis
- Nmap for controlled network scanning tests

Current addressing:

| System | IP address | Role |
|---|---|---|
| OPNsense LAN | `192.168.XX.XX` | SOC-LAN gateway |
| Windows Test VM | `192.168.XX.XX` | monitored endpoint / test source |
| Ubuntu / Wazuh | `192.168.XX.XX` | SIEM and log collector |
| Windows Host | `192.168.XX.XX` | VirtualBox host |

## Detection Pipeline

The current detection pipeline works end-to-end:

```text
Windows Test VM
       |
       v
    OPNsense
       |
       v
    Suricata
       |
       v
 EVE JSON / Syslog
       |
       v
    rsyslog
       |
       v
 /var/log/opnsense.log
       |
       v
      Wazuh
       |
       v
Custom Decoder + Rules
       |
       v
   Wazuh Alert
```
## Implemented Detections

### ICMP Test Detection

Custom Suricata rule:

```text
SID: 1000001
Signature: SOC LAB ICMP TEST
```
This rule is used to verify the complete detection pipeline from network traffic to a Wazuh alert.
The test confirms that:
- Suricata inspects the traffic
- the alert is generated
- the event is forwarded using EVE JSON / syslog
- rsyslog receives the event
- Wazuh decodes the event
- a custom Wazuh rule generates the final alert

### TCP SYN Scan Detection

Custom Suricata rule:

```text
SID: 1000002
Signature: SOC LAB POSSIBLE TCP SYN SCAN
```

The rule detects multiple TCP SYN packets from the same source within a short time window.

The detection was tested using Nmap:

```powershell
nmap -sS 192.168.XX.X
```

The scan generated a Suricata alert which was forwarded to Wazuh and processed by a dedicated Wazuh rule.

## Wazuh

Wazuh is used as the central SIEM/XDR platform in the lab.

Current functionality includes:

- Wazuh Manager
- Windows Wazuh Agent
- Sysmon integration
- endpoint event monitoring
- Suricata integration
- OPNsense syslog ingestion
- custom decoder
- custom Wazuh rules
- alert visualization in Wazuh Dashboard

Important Sysmon Event IDs currently monitored:

- Event ID 1 - Process Creation
- Event ID 3 - Network Connection
- Event ID 10 - Process Access
- Event ID 11 - File Creation
- Event IDs 12 / 13 / 14 - Registry Changes
- Event ID 22 - DNS Query

Detailed documentation:

[Wazuh documentation](wazuh/README.md)

## OPNsense

OPNsense is used as:

- firewall
- network gateway
- DHCP server
- network segmentation point
- Suricata IDS platform
- remote syslog source

Detailed documentation:

[OPNsense documentation](opnsense/README.md)

## Suricata

Suricata is used for network intrusion detection.

Current configuration includes:

- ET Open rules
- EVE JSON output
- remote syslog forwarding
- custom IDS rules
- ICMP detection
- TCP SYN scan detection
- integration with Wazuh

Detailed documentation:

[Suricata documentation](suricata/README.md)

## Network Analysis

The lab also includes packet analysis using:

- Wireshark
- tcpdump
- PCAP capture
- DNS filtering
- ICMP filtering
- TCP analysis
- TLS traffic inspection
- Follow TCP Stream

## Current Progress

Completed:

- [x] Ubuntu Server deployed
- [x] Wazuh Manager installed
- [x] Windows Wazuh Agent configured
- [x] Sysmon integrated
- [x] OPNsense deployed
- [x] SOC-LAN configured
- [x] Suricata enabled
- [x] ET Open rules configured
- [x] EVE JSON enabled
- [x] OPNsense remote syslog configured
- [x] Suricata integrated with Wazuh
- [x] custom Wazuh decoder created
- [x] custom Wazuh rules created
- [x] ICMP detection created
- [x] TCP SYN scan detection created
- [x] Nmap detection test completed
- [x] Wireshark traffic analysis
- [x] tcpdump packet capture
- [x] private configuration backup repository created

Planned:

- [ ] detailed detection reports
- [ ] additional endpoint detections
- [ ] additional network detections
- [ ] Kali Linux lab
- [ ] controlled Red Team / Purple Team exercises
- [ ] Active Directory lab
- [ ] Microsoft Sentinel / cloud security lab

## Repository Structure

```text
home-soc-lab/
├── README.md
├── architecture/
├── opnsense/
├── suricata/
├── wazuh/
├── wireshark-tcpdump/
├── detections/
└── screenshots/
```

The public repository contains documentation and sanitized examples only.

Full configuration backups are stored separately in a private repository.

## Skills Demonstrated

This project demonstrates practical experience with:

- SIEM configuration
- IDS configuration
- endpoint monitoring
- network segmentation
- network traffic analysis
- log forwarding
- EVE JSON parsing
- custom detection rules
- Linux administration
- Windows security telemetry
- troubleshooting
- incident investigation workflow
- Git and GitHub documentation
## Public Repository Security

This repository contains sanitized documentation intended for portfolio purposes.

The following information is intentionally excluded:

- exact IP addressing
- authentication data
- API tokens
- private keys
- certificates
- complete firewall backups
- full configuration exports

Sensitive configuration backups are stored separately in a private repository.
## Future Development

The next stages of the project will focus on documenting existing detections and later introducing Kali Linux for controlled Red Team and Purple Team testing.

The long-term goal is to develop the environment into a practical security lab where offensive activity can be tested against Suricata, Sysmon and Wazuh detections.
