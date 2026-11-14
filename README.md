# IDM Activation & Trial Management Script

## Features
- Trial freeze using registry protection.
- Trial and activation status persistence across IDM updates.
- Trial / activation reset utility.
- Open-source batch script.
- PowerShell support.

## Installation & Usage
1. Install a fresh copy of [Internet Download Manager](https://www.internetdownloadmanager.com/).
2. Download `zzIDMAN.cmd`.
3. Locate the file in your Downloads folder.
4. Run it with administrator privileges.
5. Follow the on-screen menu instructions.

## Available Options

**1. Activate IDM**
- Registry-based activation method.
- Currently not working.

**2. Freeze Trial**
- Attempts to preserve the 30-day trial status.
- Requires an internet connection during setup.
- Designed to persist across IDM updates.

**3. Reset Activation / Trial**
- Resets the stored activation or trial state.
- May help resolve certain activation-related errors.

## System Requirements
- Windows 7, 8, 8.1, 10 and 11.
- Windows Server equivalents.
- PowerShell functionality requires Windows 8 or later.

## Command-Line Options
- `/act` — Activation mode.
- `/frz` — Trial freeze mode.
- `/res` — Reset mode.

## Technical Overview
IDM stores trial and activation information in Windows Registry keys. This script interacts with those entries to modify the stored activation and trial state.

**Note:** This is an unofficial third-party utility. Use caution when running scripts that modify Windows Registry settings.
