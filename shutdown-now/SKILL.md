---
name: shutdown-now
description: Save the current project, then shut down Windows/macOS when asked to 关机 or via $shutdown-now.
---

Explicit shutdown requests or standalone `$shutdown-now` authorize execution; editing/discussion does not.

Save the current project, if any, using available file/editor tools; finish active writes. Reuse confirmed saved state. If saving cannot be confirmed, stop. No commits/pushes.

Briefly report saving and warn other unsaved apps may lose data. Run once for the execution host's OS from context. No reconfirmation, broad scans, polling, or retries.

Windows (PowerShell):

```powershell
& "$env:SystemRoot\System32\shutdown.exe" /s /t 0
```

macOS:

```sh
sudo -n /sbin/shutdown -h now
```

On unsupported OS or execution error, stop and report. Never collect passwords or change sudoers.
