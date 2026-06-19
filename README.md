# Windows-RDP-Authentication-Investigation

## Objective

The objective of this investigation was to analyze the Windows Remote Desktop Protocol (RDP) authentication process by correlating multiple Windows event logs and understanding how successful remote logins are recorded in Windows environments.

This investigation focused on:

- Remote Desktop Protocol (RDP)
- Windows Security Event ID 4624
- Terminal Services Event ID 1149
- Terminal Services Event ID 261
- Authentication Analysis
- Event Correlation
- Threat Hunting
- SOC Investigation Workflow
- MITRE ATT&CK Mapping

---

## Investigation Scenario

Remote Desktop Protocol (RDP) is widely used for remote administration and system management but is also a common target for attackers attempting unauthorized access and lateral movement.

Windows records RDP activity across multiple log sources. By correlating these events, analysts can reconstruct the complete authentication lifecycle from the initial connection request to a successful remote interactive logon.

In this lab, an RDP session was established from the host machine to a Windows virtual machine, and the generated authentication events were analyzed.

---

## Lab Environment

| Component | Details |
|------------|----------|
| Operating System | Windows 10 Pro (22H2) |
| Log Sources | Windows Security & Terminal Services Logs |
| Tool Used | Event Viewer |
| Protocol | Remote Desktop Protocol (RDP) |
| Default Port | TCP 3389 |
| Investigation Type | Successful Remote Authentication Analysis |

---

## Attack Simulation

A Remote Desktop session was initiated from the host machine using **Remote Desktop Connection (`mstsc`)**.

The connection successfully authenticated against the Windows virtual machine and generated multiple Windows events representing different stages of the RDP authentication process.

---

## Logs Used

### Windows Security Log

| Event ID | Description |
|------------|----------------|
| **4624** | Successful Logon |

### Terminal Services Logs

| Event ID | Description |
|------------|------------------------------|
| **261** | Listener received incoming RDP connection |
| **1149** | User authentication succeeded |

---

## Log Locations

```text
Event Viewer
├── Windows Logs
│    └── Security
│
└── Applications and Services Logs
     └── Microsoft
          └── Windows
               └── TerminalServices-RemoteConnectionManager
                    └── Operational
```

## Event Details Observed

### Event ID 261

| Field | Value |
|------------|----------------|
| Event ID | 261 |
| Description | Listener received incoming RDP connection |

---

### Event ID 1149

| Field | Value |
|------------|----------------|
| Event ID | 1149 |
| User | Dell |
| Source Network Address | 192.168.1.9 |
| Description | User authentication succeeded |

---

### Event ID 4624

| Field | Value |
|------------|----------------|
| Event ID | 4624 |
| Logon Type | 10 (Remote Interactive) |
| Account Name | Dell |
| Source Network Address | 192.168.1.9 |
| Authentication Package | NTLM |

---

## Investigation Workflow

### Step 1 — Verify RDP Listener

Confirm that the RDP service is listening on TCP port **3389**.

```cmd
netstat -ano | findstr 3389
```

---

### Step 2 — Verify Active RDP Session

Check active sessions:

```cmd
query session
```

Confirm that an active **rdp-tcp#1** session exists.

---

### Step 3 — Review Terminal Services Logs

Navigate to:

```text
Applications and Services Logs
→ Microsoft
→ Windows
→ TerminalServices-RemoteConnectionManager
→ Operational
```

Review:

- Event ID 261
- Event ID 1149

---

### Step 4 — Review Security Logs

Navigate to:

```text
Windows Logs
→ Security
```

Filter by:

```text
4624
```

Verify that the event contains:

- Logon Type 10
- Account Name
- Source Network Address
- Authentication Package

---

## Analysis

The investigation successfully reconstructed the complete RDP authentication lifecycle.

The following sequence was observed:

```text
261
↓
Listener receives incoming RDP connection

1149
↓
User authentication succeeds

4624 (Logon Type 10)
↓
Windows creates Remote Interactive logon session
```

This sequence confirms a successful Remote Desktop authentication.

### Key Observations

- Successful RDP connection established
- Authentication successfully completed
- Windows generated Event ID 4624 with Logon Type 10
- Source IP address identified
- Remote Interactive session created

---

## Indicators Observed

| IOC Type | Value |
|------------|----------------|
| Event ID | 261 |
| Event ID | 1149 |
| Event ID | 4624 |
| Logon Type | 10 |
| Username | Dell |
| Source IP | 192.168.1.9 |
| Protocol | Remote Desktop Protocol |

---

## MITRE ATT&CK Mapping

| Technique ID | Technique |
|------------|--------------------------------|
| **T1021.001** | Remote Services: Remote Desktop Protocol |

### Tactics

- Initial Access
- Lateral Movement

### Why It Matters

Threat actors frequently abuse Remote Desktop Protocol to gain remote access and move laterally inside enterprise environments. Monitoring RDP authentication events helps identify suspicious remote activity and unauthorized access attempts.

---

## Detection Logic

Monitor Windows Security and Terminal Services logs for indicators of Remote Desktop Protocol (RDP) activity.

Generate alerts for:

- Event ID **261**
- Event ID **1149**
- Event ID **4624** with **Logon Type 10**
- Unexpected source IP addresses
- Remote logins outside business hours
- Privileged account RDP activity

Correlating **261 → 1149 → 4624 (Logon Type 10)** provides high-confidence evidence of successful Remote Desktop authentication.

---

## Escalation Severity

| Severity | Reason |
|------------|----------------------------------------------|
| Low | Expected administrative RDP activity |
| Medium | Unexpected remote login requiring validation |
| High | Remote login from an unknown or suspicious source |
| Critical | RDP activity associated with compromise or lateral movement |

### Lab Assessment

**Severity: Medium**

---

## False Positives

Potential legitimate sources include:

- System administrators
- IT support personnel
- Remote maintenance
- Approved remote management activities

---

## Recommended Containment

Validate the user account and source IP address, terminate unauthorized RDP sessions, disable compromised accounts, restrict Remote Desktop access to trusted systems, and investigate the endpoint for signs of lateral movement or additional malicious activity.

---

### SIEM Detection

Generate alerts for:

- Event ID 261
- Event ID 1149
- Event ID 4624 (Logon Type 10)
- Unexpected RDP logins
- After-hours remote authentication
- Privileged account RDP usage

### Threat Hunting

Hunt for:

- Remote Interactive logons
- Unusual source IP addresses
- Repeated RDP sessions
- Privileged account RDP activity
- RDP activity followed by privilege escalation or lateral movement

---

## Key Findings

- Verified RDP listener availability on TCP port 3389.
- Confirmed an active Remote Desktop session.
- Analyzed Event ID 261 (connection received).
- Analyzed Event ID 1149 (authentication success).
- Verified Event ID 4624 with Logon Type 10.
- Correlated multiple Windows log sources to reconstruct the complete RDP authentication lifecycle.
- Mapped the activity to MITRE ATT&CK T1021.001.

---

## Conclusion

By correlating Terminal Services events (261 and 1149) with Windows Security Event ID 4624 (Logon Type 10), analysts can accurately reconstruct successful Remote Desktop authentication activity. Multi-source event correlation improves visibility into remote access behavior and supports effective SOC investigations, threat hunting, and incident response.

---

## Skills Demonstrated

- Windows Event Log Analysis
- RDP Authentication Investigation
- Event Correlation
- SOC Investigation Workflow
- Threat Hunting
- MITRE ATT&CK Mapping
- Detection Engineering
- Security Monitoring
