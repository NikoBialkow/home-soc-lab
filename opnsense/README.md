# OPNsense

## Purpose

OPNsense is used as the firewall and network security gateway in the Home SOC Lab.

Its main role is to control traffic between the lab networks and provide a platform for network security monitoring.

## Role in the Lab

OPNsense is responsible for:

- routing traffic between virtual networks
- firewall rule management
- network segmentation
- monitoring traffic between hosts
- hosting Suricata IDS/IPS
- providing visibility into network communication

## Environment

OPNsense is deployed as a virtual machine in VirtualBox.

The firewall is positioned between the lab systems and the external network.

The environment includes:

- Ubuntu Server
- Windows 10
- Wazuh
- Suricata
- VirtualBox networking

## Configuration

The initial OPNsense configuration included:

- assigning network interfaces
- configuring LAN connectivity
- configuring firewall rules
- enabling Suricata
- verifying connectivity between virtual machines

## Firewall

Firewall rules are used to control communication between systems inside the lab.

The configuration is intended to provide enough connectivity for monitoring and testing while still allowing traffic to be restricted when needed.

## Suricata Integration

Suricata is running directly on OPNsense and is used to inspect network traffic for suspicious activity.

Detailed Suricata configuration is documented here:

[Suricata documentation](../suricata/README.md)

## Troubleshooting

During the lab setup, network connectivity and monitoring configuration required verification of:

- assigned interfaces
- firewall rules
- virtual network settings
- routing
- Suricata interface selection

These troubleshooting steps helped confirm that OPNsense was correctly positioned inside the lab network.

## Future Improvements

Planned improvements include:

- more restrictive firewall rules
- additional network segmentation
- VLAN configuration
- logging and monitoring improvements
- testing blocked and allowed traffic
