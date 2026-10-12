# Lab Architecture

## Overview

The Home SOC Lab is built in VirtualBox and contains multiple systems used for network monitoring, endpoint monitoring, intrusion detection and security event analysis.

The environment is designed so that test traffic can be generated inside the lab and observed by OPNsense, Suricata and Wazuh.

## Current Architecture

```text
                         Internet
                            |
                            |
                    VirtualBox NAT
                            |
                         OPNsense
                    WAN: 10.0.2.15
                    LAN: 192.168.50.1
                            |
                            |
                        SOC-LAN
                     192.168.50.0/24
                            |
              +-------------+-------------+
              |                           |
              |                           |
      Windows Test VM                Other Lab VMs
      192.168.50.108
              |
              |
       monitored traffic
              |
              v
          Suricata
       running on OPNsense
              |
              |
        EVE JSON / Syslog
              |
              v
      Ubuntu / Wazuh Server
         192.168.1.20
              |
              v
        Wazuh Dashboard
```

The main Windows host runs VirtualBox and is connected to the home network:

```text
Windows Host
192.168.1.11
```

## Main Components

### OPNsense

OPNsense provides:

- firewalling
- routing
- DHCP
- network segmentation
- Suricata IDS
- remote syslog forwarding

Interfaces:

```text
WAN: em0
Address: 10.0.2.15/24

LAN: em1
Address: 192.168.50.1/24
```

The LAN interface creates the isolated SOC-LAN network.

## Windows Test VM

The Windows test endpoint is connected to SOC-LAN.

Address:

```text
192.168.50.108
```

It is used to:

- generate controlled network traffic
- test detection rules
- run Nmap scans
- generate Sysmon telemetry
- verify Wazuh alerts

## Ubuntu / Wazuh Server

The Ubuntu Server is connected to the home network.

Address:

```text
192.168.1.20
```

Main services:

- Wazuh Manager
- Wazuh Dashboard
- rsyslog
- Suricata log ingestion
- custom Wazuh decoders
- custom detection rules

## Detection Flow

The current network detection pipeline is:

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
Custom Decoder
       |
       v
 Custom Rule
       |
       v
   Wazuh Alert
```

## Network Segments

### Home Network

```text
192.168.1.0/24
```

Important systems:

```text
192.168.1.11 - Windows host
192.168.1.20 - Ubuntu / Wazuh
```

### SOC-LAN

```text
192.168.50.0/24
```

Important systems:

```text
192.168.50.1   - OPNsense gateway
192.168.50.108 - Windows test VM
```

## Traffic Monitoring

Suricata monitors traffic on:

```text
em1
```

This allows traffic from SOC-LAN to be inspected before alerts are forwarded to Wazuh.

## Current Detection Sources

The lab currently collects security telemetry from:

- Windows Sysmon
- Wazuh Agent
- OPNsense
- Suricata
- rsyslog
- network packet captures

## Future Architecture

Future expansion may include:

- Kali Linux
- Windows Server
- Active Directory
- vulnerable Linux hosts
- additional network segments
- Red Team and Purple Team testing
