# IDMAN Utility

A single-file batch utility for managing Internet Download Manager on Windows: locate the installation, launch and close it, run read-only diagnostics, and manage trial/activation state — all from one menu.

Maintained by ZERISO — https://github.com/zzeriso/IDMAN

## Features

- Automatic IDM detection from the registered `ExePath`, with fallbacks to the standard Program Files locations.
- Live status header showing install path, version, stored serial and trial-freeze timestamp.
- Launch and force-close IDM without hunting for shortcuts or Task Manager.
- Read-only diagnostics: OS build, architecture, elevation state, PowerShell availability, Null service state, IDM process count, CLSID entry count, key presence and temp writability — written to a report file.
- Trial freeze using registry protection.
- Trial and activation state persistence across IDM updates.
- Trial / activation reset with automatic registry backups.
- Registry access is taken over per CLSID key so IDM cannot rewrite its own markers.
- Self-repairing: converts LF line endings to CRLF and restores the trailing blank line, then restarts itself.
- Open-source batch script with PowerShell support.

## Installation & Usage

1. Install a fresh copy of [Internet Download Manager](https://www.internetdownloadmanager.com/).
2. Download `zzIDMAN.cmd`.
3. Place it in a permanent folder — not inside an archive, and not in `%TEMP%`.
4. Right-click and choose **Run as administrator**.
5. Pick an option from the menu.

The script re-launches itself as x64 (or ARM64) and elevates automatically, so a plain double-click also works once UAC is confirmed.

## Available Options

**1 — Launch Internet Download Manager**
Starts IDM from the detected install path. Reports a clear error if Windows refuses to start it.

**2 — Close IDM**
Force-closes `IDMan.exe` and confirms whether it was running.

**3 — Refresh installation detection**
Re-scans the registry and standard paths, then reports whether IDM was found.

**4 — Run system and IDM diagnostics**
Prints the checks listed above and saves the same data to a timestamped report in `%TEMP%`. Nothing is modified.

**5 — Activate IDM**
Generates random registration details and a serial, writes them to the `DownloadManager` key, locks the IDM CLSID keys, and triggers sample downloads so IDM recreates its own entries.
- Registry-based activation method.
- Currently unreliable — IDM may still show the fake serial nag.

**6 — Freeze Trial**
Locks the CLSID keys that carry the 30-day trial counter so the countdown stops advancing.
- Requires an internet connection during setup.
- Designed to survive IDM updates.
- The recommended option while activation is unreliable.

**7 — Reset Activation / Trial**
Exports the CLSID keys to `%SystemRoot%\Temp`, deletes the stored activation and trial values, and re-adds the `AdvIntDriverEnabled2` key.
- May help resolve activation-related errors.
- Backups are timestamped and can be re-imported by hand.

**8 — Open official IDM download page**
**9 — Open official IDM website**
**0 — Exit**

## Command-Line Options

- `/act` — Activation mode, unattended.
- `/frz` — Trial freeze mode, unattended.
- `/res` — Reset mode, unattended.

Internal switches, normally passed automatically:

- `-el` — Marks the process as already elevated, preventing a relaunch loop.
- `-qedit` — Disables QuickEdit and relaunches through `conhost.exe`.
- `-crlf` — One-shot guard for the line-ending self-repair.

Unattended runs close on their own after a two-second delay.

## System Requirements

- Windows 7, 8, 8.1, 10 and 11.
- Windows Server equivalents.
- PowerShell 2.0 or later, with `FullLanguage` mode available.
- Administrator privileges.
- x86, x64 and ARM64 are handled; ARM64 uses the appropriate emulation host.

## Technical Overview

IDM keeps trial and activation data in per-user `DownloadManager` registry values and in CLSID entries under the WOW6432Node (or plain `CLSID` on x86) branch of `HKCU` and `HKU\<SID>`.

The script resolves the active user SID and detects whether `HKCU` is synced with `HKU\<SID>`, so it writes to the correct hive. On activation and freeze it enumerates the CLSID branch with PowerShell and identifies IDM's own keys by shape: numeric defaults, `+`/`=` markers, a numeric `\Version` subkey, `MData`/`Model`/`scansk`/`Therad` values, or empty leaf keys. Ownership is taken and `Everyone` is granted full control before a `Deny` rule is applied, which is what stops IDM from writing to those keys again. Above 20 matching keys the script switches from locking to deleting, since locking that many is impractical.

Before touching anything, both destructive options export the CLSID keys. If something goes wrong, import the newest `_Backup_*` file from `%SystemRoot%\Temp`.

The embedded PowerShell is read from the script file itself between marker lines, so the batch stays a single portable file with no external dependencies.

## Notes

- Unofficial third-party utility. It edits `HKLM` and per-user `HKCU`/`HKU` registry branches and changes ACLs on CLSID keys.
- Run it as administrator; it aborts without elevation.
- Prefer **Freeze Trial** over **Activate** until the registration method is reliable again.
- If IDM shows a registration popup after a freeze, reinstall IDM and freeze again.
- Do not run it from inside a `.zip` or from `%TEMP%` — the script detects both and stops.
- Keep the file CRLF-terminated with one empty line at the end; if you break that, the script repairs it and restarts on its own.
