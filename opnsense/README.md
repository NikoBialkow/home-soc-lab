# OPNsense

## Purpose

OPNsense is used as the firewall, network gateway and security control point in the Home SOC Lab.

It provides:

- network segmentation
- routing
- DHCP
- firewall enforcement
- network monitoring
- Suricata IDS integration
- remote log forwarding

## Role in the Lab

OPNsense separates the test environment from the rest of the network and provides a controlled location for traffic inspection.

Its main responsibilities include:

- routing traffic between lab segments
- enforcing firewall rules
- providing network services for test systems
- hosting Suricata IDS
- forwarding security events to the SIEM
- supporting controlled detection testing

## Deployment

OPNsense is deployed as a virtual machine in VirtualBox.

The environment contains:

- an external-facing network interface
- an internal SOC-LAN interface
- an isolated test network for monitored systems

Detailed addressing information and full configuration backups are intentionally not included in the public repository.

## Network Segmentation

The SOC-LAN network is used for:

- monitored endpoints
- test systems
- controlled scanning
- IDS validation
- future Red Team / Purple Team exercises

OPNsense acts as the gateway for this segment.

## Firewall

Firewall rules are used to control communication between the lab network and other network segments.

During setup, the following areas were verified:

- gateway connectivity
- traffic forwarding
- required outbound connectivity
- internal network access
- Suricata traffic visibility
- remote log delivery

## Suricata IDS

Suricata runs directly on OPNsense and monitors traffic from the internal lab network.

Current functionality includes:

- ET Open rules
- EVE JSON output
- custom local rules
- network alert generation
- remote syslog forwarding
- integration with Wazuh

Custom detections currently include:

```text
SID 1000001
SOC LAB ICMP TEST
```

and:

```text
SID 1000002
SOC LAB POSSIBLE TCP SYN SCAN
```

More information:

[Suricata documentation](../suricata/README.md)

## Remote Logging

Suricata events are forwarded from OPNsense to the Ubuntu/Wazuh server using remote syslog.

The logging pipeline is:

```text
OPNsense
   |
   v
Suricata
   |
   v
EVE JSON / Syslog
   |
   v
Ubuntu rsyslog
   |
   v
Wazuh
```

The public repository intentionally omits exact destination addresses and environment-specific configuration values.

## EVE JSON

EVE JSON is used because it provides structured fields that can be parsed by Wazuh.

Examples include:

- source address
- destination address
- source port
- destination port
- protocol
- signature ID
- signature name
- severity
- flow information

This makes it possible to create custom Wazuh detection rules based on Suricata events.

## Troubleshooting

Several issues were encountered during the initial setup.

### Suricata running but no alerts

Suricata was active, but alerts were initially not generated correctly.

The following areas were verified:

- monitored interface
- IDS service status
- active rules
- rule loading
- network traffic
- EVE output
- log forwarding

### Custom rule parsing error

One custom rule contained an invalid action:

```text
lert
```

instead of:

```text
alert
```

The issue was identified through Suricata logs and corrected.

This was a useful example of troubleshooting a rule parsing problem based on log output.

### Remote syslog troubleshooting

Remote logs initially did not appear on the Ubuntu/Wazuh server.

Troubleshooting included:

- verifying UDP syslog transport
- checking rsyslog listener status
- using tcpdump to confirm packet delivery
- verifying source translation through the virtualization layer
- adjusting the rsyslog filter

This confirmed that the problem was related to the source address seen after virtualization/NAT.

## Testing

The OPNsense and Suricata configuration has been tested using:

- ICMP traffic
- Nmap SYN scans
- packet inspection
- Suricata alert validation
- remote syslog verification
- Wazuh alert verification

Example controlled test:

```powershell
nmap -sS <LAB_GATEWAY>
```

The scan generated the custom TCP SYN scan detection.

## Backup

A full OPNsense XML configuration backup is stored separately in a private repository.

The public repository contains documentation and sanitized examples only.

## Security Considerations

The following information is intentionally excluded from this public repository:

- exact host addresses
- full DHCP configuration
- authentication data
- certificates
- private keys
- full firewall rulesets
- complete OPNsense configuration backups

## Future Improvements

Planned improvements include:

- additional network segments
- additional IDS detections
- stricter firewall policies
- Kali Linux integration
- Red Team / Purple Team scenarios
- possible VLAN-based segmentation
- migration to a dedicated virtualization platform in the future
