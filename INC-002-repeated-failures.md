# INC-002 — Repeated failed Windows authentication (controlled correlation test)

**Disposition:** Expected lab activity / detection validation  
**Endpoint:** `SOC-Windows11`  
**Test account:** `SOC-LabTest`  
**Source:** Windows Security event 4625, standard Wazuh rule 60122  
**Correlation detection:** Custom rule **100102**, level **10**  
**Description:** `LAB: Multiple failed Windows logons for same user`  
**MITRE mapping:** T1110 (Brute Force) — behavioral mapping, not proof of a real attack

## Objective

Check whether the SIEM raises an alert when repeated invalid sign-in attempts target the same lab account within a short interval.

## Test steps and observations

1. A dedicated non-administrator lab account `SOC-LabTest` was created and verified through a successful run as that user.
2. Invalid credentials were supplied intentionally using PowerShell credential-based process launching.
3. Windows logged event ID **4625**; the corresponding Wazuh record identified `targetUserName = SOC-LabTest`.
4. The operator tested repeated failed logons; Wazuh produced **one correlation alert** with custom rule ID **100102**, severity level **10**.
5. The rule's configured lab threshold was three failed logons within 60 seconds, grouped by target username. The screenshot confirms the correlation alert, but not an exhaustive raw-event timeline.

## Analysis

This is a successful rule-validation test in a controlled environment. The observed correlation does not establish malicious intent. An analyst reviewing an unexpected alert should investigate the source address or host, account, timeline, lockout status, and any subsequent successful login.

## Response / outcome

No production remediation was required. Alert was classified as authorized test activity; detection engineering milestone completed.

## Evidence

- [Custom rule 100102 alert](../screenshots/05-bruteforce-correlation.png)

## Suggested follow-up

Inspect the underlying 4625 records and add a timestamp-based timeline if higher evidentiary rigor is desired.
