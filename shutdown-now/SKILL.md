---
name: shutdown-now
description: Immediately shut down the current Windows or macOS host on explicit request.
---

Require actual shutdown authorization; editing/discussion is not authorization. Briefly warn of unsaved-data loss. Use the execution host's OS from context; run once without reconfirmation, preflight, polling, or retries.

Windows (PowerShell):

```powershell
& "$env:SystemRoot\System32\shutdown.exe" /s /f /t 0
```

macOS:

```sh
sudo -n /sbin/shutdown -h now
```

On unsupported OS or execution error, stop and report. Never collect passwords or change sudoers.
