# Lab Architecture and Data Flow

```text
[Windows 11 Pro: EAGLESPK / SOC-Windows11]
  Windows Security (4625) ----+
  Sysmon Operational (1) ----+--> Wazuh agent 4.14.10
                                     |
                            TCP 1514 (host-only LAN)
                                     v
                 [Wazuh all-in-one VirtualBox appliance]
                   Manager + custom local_rules.xml
                                     |
                                  Indexer
                                     |
                          Dashboard / Threat Hunting
```

The server was deployed with NAT and Host-Only networking in VirtualBox. The Wazuh agent used the host-only interface to communicate with the server. Dashboard access used HTTPS. This architecture represents an authorized local training environment, not a production SOC.

## Event pipeline and verification

1. Windows records logon failures in its Security event channel (4625).
2. Sysmon records process starts in `Microsoft-Windows-Sysmon/Operational` (event 1).
3. The Wazuh agent subscribes to the Sysmon event channel using `eventchannel` and sends monitored activity to the server.
4. Wazuh Manager generates standard alerts (60122) or custom alerts (100101, 100102) as matching rules are evaluated.
5. The analyst filters alerts by `agent.name` and `rule.id` in Threat Hunting.

**Limitation:** Threat Hunting shows alert records, not every collected Windows/Sysmon event; the lab did not prove archival of all raw telemetry.
