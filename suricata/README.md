# Suricata IDS

## Purpose

Suricata is used as the network intrusion detection system in the Home SOC Lab.

It runs on OPNsense and inspects traffic passing through the internal lab segment.

The main goals are to:

- detect suspicious network activity
- validate custom detection rules
- generate structured security events
- forward alerts to Wazuh
- support controlled detection testing

## Role in the Lab

Suricata is positioned at the network gateway so that traffic from monitored lab systems can be inspected before leaving the internal test segment.

The detection flow is:

```text
Test System
    |
    v
 OPNsense
    |
    v
 Suricata
    |
    v
 EVE JSON
    |
    v
 Remote Syslog
    |
    v
 Ubuntu / rsyslog
    |
    v
 Wazuh
```

## Rulesets

The environment currently uses:

- ET Open rules
- custom local Suricata rules

Custom rules are stored separately in the private backup repository.

Sanitized examples are documented in this public repository.

## EVE JSON

EVE JSON output is enabled because it provides structured event data that can be consumed by Wazuh.

Example fields include:

- source address
- destination address
- source port
- destination port
- protocol
- event type
- signature ID
- signature name
- severity
- flow statistics

This structured format allows Wazuh to process Suricata events more effectively than plain-text alerts.

## Custom Detection 1 - ICMP Test

The first custom rule was created to verify the complete IDS-to-SIEM pipeline.

```text
SID: 1000001
Signature: SOC LAB ICMP TEST
```

Purpose:

- verify that Suricata sees traffic
- verify that custom rules are loaded
- verify remote log forwarding
- verify Wazuh decoding
- verify custom Wazuh alert generation

The rule detects ICMP echo request traffic.

This detection is primarily a validation rule and is not intended to represent a high-severity security event.

## Custom Detection 2 - TCP SYN Scan

The second custom rule detects a burst of TCP SYN packets from the same source.

```text
SID: 1000002
Signature: SOC LAB POSSIBLE TCP SYN SCAN
```

The rule uses:

- TCP traffic
- SYN flag matching
- source-based tracking
- thresholding within a short time window

This makes it useful for detecting controlled scanning activity in the lab.

## Detection Logic

The custom SYN scan rule is conceptually based on:

```text
Multiple TCP SYN packets
        |
        v
Same source host
        |
        v
Short time window
        |
        v
Threshold exceeded
        |
        v
Suricata alert
```

This detection was tested using Nmap:

```powershell
nmap -sS 192.168.XX.XX
```

The scan generated a Suricata event which was then forwarded to Wazuh.

## Alert Flow

When a custom Suricata rule is triggered:

```text
Traffic
  |
  v
Suricata Rule Match
  |
  v
EVE JSON Alert
  |
  v
Remote Syslog
  |
  v
Ubuntu rsyslog
  |
  v
Wazuh Decoder
  |
  v
Wazuh Rule
  |
  v
Wazuh Alert
```

## Integration with Wazuh

Suricata alerts are forwarded to the Wazuh server using EVE JSON over remote syslog.

Wazuh then:

- identifies the Suricata event
- parses the JSON
- extracts structured fields
- evaluates custom Wazuh rules
- generates an alert

More information:

[Wazuh documentation](../wazuh/README.md)

## Troubleshooting

Several issues were encountered during setup.

### Rule file not being loaded

At one stage, the custom rule file existed but it was unclear whether it was included in the active Suricata ruleset.

Troubleshooting included:

- checking active rule directories
- verifying rule file references
- inspecting Suricata configuration
- testing configuration before restart

### Invalid rule action

A typo was introduced into a custom rule:

```text
lert
```

instead of:

```text
alert
```

Suricata reported a rule parsing error.

This demonstrated the importance of validating custom rules before relying on them.

### No alerts generated

When alerts were missing, the following were checked:

- Suricata process status
- monitored interface
- packet visibility
- active rules
- EVE output
- remote logging
- rule syntax

Packet capture was used to confirm that traffic was reaching the monitored network segment.

## Testing

The current Suricata deployment has been tested with:

- ICMP traffic
- TCP SYN scanning
- Nmap
- tcpdump
- remote syslog
- Wazuh alert generation

## Security Considerations

The public repository intentionally does not contain:

- full Suricata configuration
- exact network addressing
- complete private rulesets
- credentials
- authentication data
- sensitive infrastructure information

Full configuration backups are stored in a separate private repository.

## Current Status

Completed:

- [x] Suricata deployed on OPNsense
- [x] ET Open rules enabled
- [x] EVE JSON enabled
- [x] remote logging configured
- [x] Wazuh integration completed
- [x] custom ICMP rule created
- [x] custom SYN scan rule created
- [x] Nmap detection tested
- [x] alerts verified in Wazuh

## Future Improvements

Planned improvements include:

- additional scan detections
- DNS anomaly detection
- suspicious outbound traffic detection
- additional ET Open validation
- improved alert suppression / deduplication
- Red Team / Purple Team testing
- correlation with Sysmon endpoint events
