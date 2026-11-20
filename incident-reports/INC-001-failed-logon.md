# INC-001 — Failed Windows logon (controlled test)

**Disposition:** Expected lab activity / benign authentication failure  
**Endpoint:** EAGLESPK (monitored as `SOC-Windows11`)  
**Source:** Windows Security event 4625  
**Wazuh detection:** Rule 60122, level 5, `Logon Failure - Unknown user or bad password`  
**Observed time:** November 18, 2026, 23:15:07 (Windows local display)

## Observations

- Windows Security event ID **4625** was recorded.
- The event identified **logon type 2** (interactive).
- `Status = 0xC000006D` indicates failed authentication.
- `SubStatus = 0xC000006A` indicates an incorrect password.
- The Wazuh alert was found in Threat Hunting for the monitored Windows agent.
- The operator deliberately made an incorrect password attempt in an authorized lab.

## Analysis

A single failed interactive logon is insufficient evidence of password spraying, brute force, or compromise. The observed event matches the deliberate test scenario.

## Response / outcome

No host isolation or account blocking was warranted. The test demonstrated end-to-end visibility from a Windows Security log into a Wazuh alert.

## Evidence

- [Wazuh event 4625](../screenshots/02-failed-logon-alert.png)

## Recommendation

For unexpected authentication failures, review recent successful logons, login source, user identity, timing and frequency before escalation.
