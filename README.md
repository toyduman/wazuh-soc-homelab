# Home SOC Lab — Wazuh, Windows 11 and Sysmon

A hands-on blue-team lab focused on endpoint telemetry collection, Windows authentication monitoring, custom SIEM detections, and incident triage.

> **Scope:** Personal, authorized lab only. All test activity was controlled. The alerts in this repository are not evidence of a real-world attack.

## Lab architecture

```text
Windows 11 host (SOC-Windows11)
  | Windows Security log: Event ID 4625
  | Sysmon Operational log: Event ID 1
  | Wazuh agent 4.14.10
  | TCP 1514 (agent to manager)
  v
Wazuh all-in-one virtual appliance (VirtualBox)
  | Manager: log analysis + local custom rules
  | Indexer: searchable alerts
  | Dashboard: Threat Hunting / Events
```

The Wazuh server was reachable over the VirtualBox host-only network. Local IP addresses are intentionally excluded from the public report.

## Tools

- Windows 11 Pro and Windows Event Logs
- Microsoft Sysmon
- Wazuh Manager, Agent and Dashboard (agent version observed: 4.14.10)
- Oracle VirtualBox, PowerShell, SSH

## Completed milestones

| Test | Evidence | Result |
|---|---|---|
| Endpoint enrolment | [Active agent](screenshots/01-windows-agent-active.png) | `SOC-Windows11` registered and active |
| Failed interactive logon | [Wazuh event](screenshots/02-failed-logon-alert.png) | Security event 4625 matched Wazuh rule 60122 (level 5) |
| Sysmon process creation | [Windows event](screenshots/03-sysmon-event-1.png) | Sysmon Event ID 1 recorded Notepad process creation |
| Custom process-creation alert | [Wazuh alerts](screenshots/04-custom-notepad-rule.png) | Custom rule 100101 (level 3), four matching alerts observed |
| Failed-logon correlation | [Wazuh alert](screenshots/05-bruteforce-correlation.png) | Custom rule 100102 (level 10) fired on the controlled repeated-failure test |

## More evidence

- [Wazuh detection rules in editor](screenshots/06-wazuh-custom-rules-xml.png)
- [MITRE technique and correlation settings](screenshots/07-bruteforce-mitre-mapping.png)
- [Windows event status and substatus](screenshots/08-failed-logon-event-details.png)
- [Sysmon collection configuration](screenshots/09-sysmon-agent-configuration.png)
- [Lab account and target user evidence](screenshots/10-soc-labtest-target-username.png)
- [Evidence index](screenshots/README.md)
- [Architecture and collection flow](architecture.md)

## Detection rules

Example XML definitions corresponding to the observed custom alert IDs are in [`detection-rules/`](detection-rules/). Before deploying, compare them with the actual server file at `/var/ossec/etc/rules/local_rules.xml`. A screenshot from the source PDF also shows the local rule definitions in the editor. The XML included here is a transcribed example, not an independently retrieved live configuration file.

- **100101 — Notepad process creation:** training detection for Sysmon Event ID 1; ordinary Notepad use is benign.
- **100102 — Repeated failed Windows logons:** correlation of failed Windows authentication events for a matching username. MITRE ATT&CK: [T1110, Brute Force](https://attack.mitre.org/techniques/T1110/). The test illustrates a pattern, not a confirmed malicious attack.

## Investigations

1. [INC-001 — Failed Windows logon](incident-reports/INC-001-failed-logon.md)
2. [INC-002 — Repeated authentication failures](incident-reports/INC-002-repeated-failures.md)

## Analyst skills demonstrated

- Connecting Windows endpoint logs to a SIEM
- Investigating Event ID 4625, logon types, and status/substatus codes
- Enabling Sysmon event collection and validating process creation telemetry
- Writing and validating a custom Wazuh rule
- Correlating multiple authentication failures and distinguishing an alert from a confirmed incident
- Collecting evidence and documenting a disposition

## Limitations and next steps

- Only one Windows endpoint was monitored.
- No real adversary activity was conducted or claimed.
- For a production deployment: use TLS certificates and hardened credentials, review alert tuning, add more endpoints, and validate each rule with repeatable test cases.
- Future work: process ancestry, additional Sysmon telemetry, and investigation timelines.

## Security note

Never commit Wazuh credentials, API tokens, original agent enrollment keys, full configuration files with secrets, or unredacted personal information. This repository contains sanitized documentation and selected screenshots only.
