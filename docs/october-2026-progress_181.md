# October 2026 Lab Progress — Network Segmentation and Detection

> **Scope:** Isolated, self-hosted VirtualBox cybersecurity lab. This document records completed exercises and observed results; it is not a claim of production deployment or a completed penetration test.

## Summary

During October 2026, the lab was expanded with separate attacker and target network segments routed through OPNsense. Kali Linux and Metasploitable 2 were used to generate controlled test traffic, while Suricata and Wazuh were used to investigate network alerts and telemetry. The focus remains on detection, log analysis, and understanding the relationship between network traffic and alerts.

## Architecture

| Component | Role | Network / address |
| --- | --- | --- |
| OPNsense | Firewall, routing and Suricata sensor | Gateway `192.168.60.1` on RED_ATTACK; `192.168.70.1` on RED_TARGET |
| Kali Linux | Controlled traffic generator | `RED_ATTACK`: `192.168.60.10/24` |
| Metasploitable 2 | Intentionally vulnerable lab target | `RED_TARGET`: `192.168.70.0/24` (exact host IP not documented here) |
| Ubuntu Server | Wazuh and rsyslog | Home SOC environment |
| Windows 10 endpoint | Sysmon event source | Home SOC environment |

### Logical traffic path

```text
RED_ATTACK (192.168.60.0/24)
    Kali Linux (192.168.60.10)
              |
       OPNsense firewall
        Suricata sensor
              |
RED_TARGET (192.168.70.0/24)
    Metasploitable 2

Suricata EVE telemetry --> log forwarding --> Wazuh --> analyst review
Windows 10 Sysmon ------> Wazuh ----------------------^ 
```

The diagram is logical rather than a full interface-level topology. The Home SOC segment is not represented as a routed attack path.

## Completed work

### 1. Network segmentation

- Created separate VirtualBox network segments for attacker and target systems.
- Configured OPNsense interfaces and gateways for both networks.
- Verified connectivity and network traffic across the lab segments.
- Configured Kali's `nftables` baseline with default-drop policies and specific outbound allowances for lab-target traffic, DNS, HTTP/HTTPS, NTP, and ICMP. Established/related connections and loopback traffic were also allowed.

**Why it matters:** This enables testing network controls and monitoring traffic that crosses the routed boundary rather than treating all VMs as a single flat network.

### 2. Suricata telemetry

- Extended the Suricata EVE JSON configuration to include additional event types: `alert`, `http`, `dns`, `tls`, `files`, `smb`, `ssh`, and `flow`.
- Worked with local ICMP detection rules.
- Investigated timestamps and event visibility during log review.

**Validation note:** An enabled EVE event type means logging was configured for that category; it does *not* prove that examples of every event type were generated and ingested. Screenshots and example redacted records should be added for each verified category.

### 3. Wazuh and endpoint telemetry

- Confirmed visibility of Suricata-generated events in the Wazuh environment after integration/configuration adjustments.
- Used Sysmon on a Windows 10 endpoint and inspected process creation telemetry (Event ID 1).
- Continued reviewing signal amid realistic background activity rather than eliminating all low-value events.

### 4. Controlled traffic and alert review

- Generated ICMP and Nmap traffic in the isolated lab.
- Observed Suricata alerts from relevant test traffic.
- Exercised SMB-related traffic as part of network validation.

**Boundary of evidence:** The above confirms controlled activity and some alert visibility; it does not claim full attack-chain detection, successful exploitation, complete event coverage, or quantified detection rates.

## Evidence checklist

Before marking any detection scenario as fully documented, add the following where available:

- [ ] Sanitized screenshot of the originating traffic/test and timestamp.
- [ ] Relevant Suricata EVE event or local rule ID, with sensitive data removed.
- [ ] Corresponding Wazuh alert or event showing the received record.
- [ ] Short explanation of what the alert proves and what it does **not** prove.
- [ ] Notes on false positives, background traffic, and detection gaps.

## Next steps

1. Document one end-to-end scenario: test traffic → Suricata EVE → Wazuh → analyst interpretation.
2. Add verified, redacted log excerpts and screenshots.
3. Make a clean topology diagram separating the Home SOC environment from the Red Team practice networks.
4. Create a dedicated Active Directory / IT Support lab repository rather than mixing its Windows Server and GPO documentation into the SOC project.

## Safety and publication notes

- Run tests only against owned lab systems or systems with authorization.
- Do not publish credentials, private keys, API tokens, identifiable personal data, or unredacted internal infrastructure secrets.
- Private lab IP ranges are included for reproducibility and do not identify publicly routable hosts.
