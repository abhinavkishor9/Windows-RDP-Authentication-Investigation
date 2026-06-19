# Installation Notes

## Lab Requirements

- Windows 10 Pro Virtual Machine
- Remote Desktop enabled
- Host system running Windows
- Event Viewer
- Administrative privileges

---

## Lab Configuration

1. Enable Remote Desktop on the VM.
2. Ensure the RDP service is running.
3. Allow inbound TCP port 3389.
4. Obtain the VM IP address using:

```cmd
ipconfig
```

5. Connect using:

```cmd
mstsc
```

6. Authenticate using the VM credentials.

---

## Verification Commands

Verify RDP Listener:

```cmd
netstat -ano | findstr 3389
```

Verify Active Session:

```cmd
query session
```

Verify Security Logs:

```text
Event Viewer
→ Windows Logs
→ Security
```

Verify Terminal Services Logs:

```text
Applications and Services Logs
→ Microsoft
→ Windows
→ TerminalServices-RemoteConnectionManager
→ Operational
```

The lab is ready for investigation once authentication events are generated.
