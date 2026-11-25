# IDMAN Utility

A single-file Windows batch utility for managing Internet Download Manager (IDM). It provides installation detection, installation, launch and process management, system diagnostics, and registry-based trial and activation management — all through a simple menu.

Maintained by **ZERISO** — [ZERISO](https://github.com/zzeriso)
Repo **IDMAN** Link: — [IDMAN](https://github.com/zzeriso)

## Changelog

**Version 3.4**

- Reorganized the script into numbered sections.
- Restored the WindowsApps directory to `PATH`.
- Aligned menu labels and option dispatch.
- Fixed launch failures caused by an incorrectly appended `-OutFile` argument.
- Fixed winget detection, banner formatting, duplicate diagnostic output and menu colour conflicts.
- Added direct IDM installation through winget, with an official download-page fallback.
- Improved diagnostics with HKLM registry checks and temporary-directory write tests.
- Added a startup line-ending warning.

---

## Features

- **Automatic IDM Detection:** Finds IDM through its registered `ExePath`, with standard installation paths as fallbacks.
- **Live Status Header:** Displays installation path, version, stored serial and trial-freeze timestamp.
- **Direct Installation:** Installs IDM through winget or opens the official download page as a fallback.
- **Process Management:** Launches and force-closes IDM without manually locating its executable.
- **System Diagnostics:** Checks Windows version, architecture, administrator privileges, PowerShell, Null service, IDM processes, CLSID entries, registry keys and temporary-directory access. Generates a diagnostic report.
- **Trial Freeze:** Prevents the trial countdown from advancing through registry protection.
- **Activation Management:** Provides registry-based activation and trial-state management.
- **Reset & Backup:** Resets activation and trial data after exporting registry backups.
- **Update Persistence:** Designed to preserve trial-freeze and activation changes across IDM updates.
- **Single-File Design:** Includes embedded PowerShell support without requiring external script files.

## Installation & Usage

1. Install [Internet Download Manager](https://www.internetdownloadmanager.com/) or use option **8**.
2. Download `IDMAN.cmd`.
3. Place it in a permanent folder, not inside an archive or `%TEMP%`.
4. Run the script and confirm the UAC prompt.
5. Select the required option from the main menu.

**Note:** The script automatically relaunches with the appropriate architecture and administrator privileges. A normal double-click is sufficient once UAC is confirmed.

## Available Options

**1 — Launch Internet Download Manager**

Launches IDM from its detected installation directory.

**2 — Close IDM**

Force-closes `IDMan.exe` and reports its process status.

**3 — Refresh Installation Detection**

Re-scans the registry and standard installation paths to update IDM detection.

**4 — Run System & IDM Diagnostics**

Runs read-only system and IDM checks and saves a timestamped report in `%TEMP%`.

**5 — Activate IDM**

Attempts registry-based activation by generating registration details, writing registry values and applying CLSID key restrictions.

- Currently unreliable; IDM may still display a registration warning.

**6 — Freeze Trial**

Applies registry restrictions to prevent the 30-day trial countdown from advancing.

- Requires an internet connection during setup.
- Designed to persist across IDM updates.
- Recommended over the activation option while activation remains unreliable.

**7 — Reset Activation / Trial**

Backs up the relevant CLSID registry keys, removes stored activation and trial values, and restores the `AdvIntDriverEnabled2` key.

- May resolve activation-related issues.
- Backups are saved with timestamps in `%SystemRoot%\Temp`.

**8 — Direct Install IDM [winget]**

Installs IDM using its exact winget package ID:

`Tonec.InternetDownloadManager`

- Automatically resolves the winget executable.
- Opens the official download page if winget is unavailable.
- Refreshes installation detection after installation.

**9 — Help — ZERISO GitHub**

Opens the project repository.

**0 — Exit**

Closes the utility.

## Command-Line Options

The following switches allow unattended execution:

| Switch | Function |
|---|---|
| `/act` | Activation mode |
| `/frz` | Trial freeze mode |
| `/res` | Reset mode |

**Internal switches:**

| Switch | Function |
|---|---|
| `-el` | Marks the process as already elevated. |
| `-qedit` | Disables QuickEdit and relaunches through `conhost.exe`. |

Unattended operations automatically close after a two-second delay.

## System Requirements

- Windows 7, 8, 8.1, 10 and 11.
- Compatible Windows Server versions.
- PowerShell 2.0 or later with FullLanguage mode.
- Administrator privileges.
- winget (App Installer) for direct installation. Optional; the official download page is used as a fallback.
- Supports x86, x64 and ARM64 architectures.

## Technical Overview

IDM stores its trial and activation information in per-user `DownloadManager` registry values and CLSID entries under the appropriate `HKCU` and `HKU` registry branches.

IDMAN identifies the active user SID and checks the corresponding registry hives before making changes. PowerShell is used to enumerate IDM-related CLSID entries and identify them through their registry structure and stored values.

The utility also handles registry ownership and permissions when applying its trial-freeze and activation operations.

Before destructive operations, relevant CLSID registry entries are exported to `%SystemRoot%\Temp` for backup and possible manual restoration.

For winget installation, the script resolves the executable through its full path because its startup process modifies `PATH`. The WindowsApps directory is preserved to maintain access to the winget alias.

PowerShell code is embedded directly inside the batch file and extracted using marker lines, keeping the utility portable and self-contained.

## Notes & Limitations

- IDMAN is an unofficial third-party utility.
- Registry modifications and permission changes are performed on Windows registry branches.
- Administrator privileges are required.
- Trial Freeze is the recommended option while registry-based activation remains unreliable.
- If IDM displays a registration warning after freezing, reinstall IDM and repeat the freeze process.
- Do not run the script from inside a ZIP archive or `%TEMP%`.
- Keep the script CRLF-terminated with one empty line at the end. IDMAN only warns about incorrect line endings; it does not automatically repair them.

**Manual line-ending repair (PowerShell):**

```powershell
$p = 'C:\path\to\IDMAN.cmd'
$t = [IO.File]::ReadAllText($p) -replace "\`r\`n","\`n" -replace "\`r","\`n"
$t = $t.TrimEnd("\`n") + "\`n\`n"
[IO.File]::WriteAllText($p, ($t -replace "\`n","\`r\`n"), [Text.Encoding]::ASCII)
```

---

**Project:** [github.com/zzeriso](https://github.com/zzeriso)
