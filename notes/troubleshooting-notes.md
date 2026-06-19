# Troubleshooting Notes

## Problem: Port 3389 not listening

### Resolution

- Enable Remote Desktop.
- Start Remote Desktop Services.
- Restart the VM if necessary.

---

## Problem: Cannot connect through RDP

### Resolution

- Verify the VM IP address.
- Confirm firewall rules allow TCP 3389.
- Verify both systems are on the same network.

---

## Problem: Event ID 4624 not generated

### Resolution

- Ensure the RDP login completed successfully.
- Verify that Logon Type is 10.
- Check the Security log immediately after authentication.

---

## Problem: Event ID 1149 not found

### Resolution

Navigate to:

```text
Applications and Services Logs
→ Microsoft
→ Windows
→ TerminalServices-RemoteConnectionManager
→ Operational
```

Verify that the Operational log is enabled.

---

## Problem: Event ID 261 not found

### Resolution

Confirm that the RDP listener accepted an incoming connection and that the Remote Desktop Services log is enabled.

---

## Problem: Logon Type 10 not observed

### Resolution

Verify that the login occurred through Remote Desktop (mstsc) rather than a local console session.

Successful RDP authentication should generate Event ID 4624 with Logon Type 10.

---

## Investigation Tip

Correlate:

- Event ID 261
- Event ID 1149
- Event ID 4624 (Logon Type 10)

to reconstruct the complete RDP authentication lifecycle.
