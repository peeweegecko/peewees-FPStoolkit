# Gaming Performance Toolkit

**By peeweegecko — for the people.**

A free, open-source Windows optimization toolkit. One app with everything in
one place: performance & latency tweaks, privacy/telemetry controls, debloat,
app & driver installers, DNS/network tools, a timer-resolution toggler, a
system-info readout, and a BIOS/XMP helper.

It's a native C++ (Win32) app — lightweight, no runtime to install, no
background services. Every change is meant to be **safe and reversible**.

---

## ⚠️ Read first — safety

This tool changes Windows settings (registry, services, power, network). That's
normal for an optimizer, but:

- **Make a System Restore Point first.** The Performance tab has a one-click
  button, and many actions offer to create one. Do it before applying tweaks.
- Tweaks are labeled **Essential** (low risk) or **Advanced** (use caution).
  Hover any tweak to see its risk level and likely performance impact.
- A few **Advanced** tweaks are hardware-dependent (they may help on one PC and
  not another). Test a few at a time rather than applying everything at once.
- Use at your own discretion. This software is provided **as-is, with no
  warranty** (see LICENSE).

---

## How to run it

### Option A — download the release (easiest)
1. Go to the **[Releases](../../releases)** page and download
   `GamingToolkit.exe`.
2. Double-click it. **Windows will warn you** because the app isn't
   code-signed (this is normal for free homemade tools):
   - **SmartScreen** ("Windows protected your PC") → click **More info → Run
     anyway**.
   - **Smart App Control** (Windows 11) may block it outright with no "run
     anyway" button. If so, you can temporarily turn it off in
     **Windows Security → App & browser control → Smart App Control**, run the
     app, then turn it back on. *(On Windows 11 24H2+ this is reversible; on
     older builds turning it off is permanent, so only do it if you're on a
     current build.)*
3. Click **Yes** at the admin (UAC) prompt — the tweaks need administrator
   rights.

### Option B — build it yourself (most trust)
The full source is here so you can read exactly what every button does before
running anything. See **Building from source** below.

---

## Why it's unsigned / flagged

A code-signing certificate costs money and reputation takes time to build, so
this free tool is unsigned — which is why Windows warns about it. The whole
point of keeping the **source public** is so you (or anyone) can verify it's
not doing anything shady. It only runs standard Windows commands
(`powercfg`, `reg`, `netsh`, `sc`, `winget`) plus the documented timer API.

---

## Features — what each tab does

| Tab | Features |
|-----|----------|
| **Performance** | Restore point, Ultimate Performance plan, disable USB power saving, turn off HW GPU scheduling, **Revert All**, **Save/Load profile** |
| **Privacy** | Minimize telemetry + disable DiagTrack service (reversible) |
| **Network** | Switch DNS to Google or Cloudflare, reset DNS to automatic (**auto-detects your active adapter**) |
| **Install Apps** | Brave, OBS, DDU, OpenRGB, 7-Zip, WinRAR, Discord, Steam, Epic, Razer Synapse, Logitech G HUB (+ onboard-memory guide) — via winget, with tooltips |
| **Runtimes** | DirectX runtime, Visual C++ Redistributables, .NET Desktop Runtime |
| **Maintenance** | Restore point, Disk Cleanup, SFC scan, DISM repair |
| **Shortcuts** | Task Manager, System Info, Event Viewer, Device Manager, Restart Explorer, reboot to BIOS |
| **Timer Resolution** | Set 0.5 ms (session), restore, check current, **Make Permanent (startup task)** + Remove Permanent |
| **Privacy / CPU & GPU / Storage / Internet / Debloat / Appearance** | Tweaks are split into themed tabs. Each is a scrollable checkbox list with Apply Selected / Select Recommended / Clear All. ★ marks recommended tweaks; hover any item to see its risk level (Essential/Advanced) and performance impact (gain / may hurt / neutral). |
| **Drivers** | Windows Update, optional updates, Device Manager, NVIDIA/AMD official pages, Intel Driver Assistant, get DDU, DDU Safe-Mode guide |
| **BIOS & Hardware** | Detects CPU/GPU/RAM, checks whether XMP/EXPO looks enabled (rated vs running RAM speed), XMP/EXPO guide, reboot-to-BIOS |
| **System Info** | Full read-only specs report (OS, board, BIOS, CPU clocks, RAM sticks/speeds, GPU+driver+resolution, storage, network); copy to clipboard; open DxDiag |
| **About** | By peeweegecko — for the people |

The **Shortcuts** tab also includes **Restart Explorer** (restarts the
taskbar/desktop `explorer.exe` process).

A **live CPU / RAM monitor** runs in the bottom-right of the window, updating
once a second.

---

## How it works

Most buttons just run a **standard Windows command** for you:
- `powercfg` → power plans / USB suspend
- `reg` → registry toggles (HW scheduling, telemetry)
- `sc` → start/stop services (DiagTrack)
- `netsh` → DNS settings
- `winget` → app and runtime installs
- `shutdown /r /fw` → reboot to BIOS

The **timer resolution** feature is real C++: it calls `NtSetTimerResolution`
from `ntdll.dll` (see `timer_resolution.cpp`).

Everything is meant to be **reversible**. The app can create a System Restore
Point so you can roll back if something doesn't feel right.

---

## Recent additions

- **No more freezing** — the app used to lock up ("not responding") during app
  installs because it blocked the UI thread waiting for the process. Now quick
  commands pump UI messages while they run, and long installs/scans open in
  their own console without blocking the app at all.
- **Tweaks tab** — a checkbox list of ~26 well-known optional tweaks split into
  Essential (low-risk) and Advanced (caution). Check what you want, click
  *Apply Selected*. *Select All* / *Clear All* included.
- **Drivers tab** — quick access to Windows Update, the optional-driver-updates
  page, Device Manager, and DDU for clean GPU driver swaps.
- **Restart Explorer** button (Shortcuts tab) — restarts `explorer.exe`.
- **About tab** — by peeweegecko, for the people.

- **Live CPU/RAM monitor** — bottom-right status pane, ticks every second
  (CPU% from `GetSystemTimes`, RAM from `GlobalMemoryStatusEx`).
- **Revert All** — one button on the Performance tab undoes every tweak
  (power plan → Balanced, USB suspend back on, GPU scheduling back on,
  telemetry/DiagTrack restored, DNS back to automatic). Installed apps stay.
- **Profiles** — *Save Profile* writes `profile.txt` next to the .exe;
  *Load + Apply Profile* re-applies it. Right now Save stores a recommended
  gaming set; an easy improvement is to drive it from live checkboxes.
- **Auto-detect network adapter** — DNS changes now target whatever adapters
  are actually up (via `GetAdaptersAddresses`), not just hard-coded names.
- **WinRAR** added alongside 7-Zip in the Install Apps tab.

## Notes & limits

- **DNS**: falls back to "Wi-Fi"/"Ethernet" only if detection finds nothing.
- **Ideas to grow this** (good learning exercises):
  - Make *Save Profile* read live checkbox states instead of a fixed set
  - Add GPU usage/temperature (needs a vendor API or WMI)
  - An FPS overlay (bigger project — needs DirectX/overlay hooking)
  - Upgrade the UI to **Dear ImGui** for a slicker look

Ask me for any of these and I'll wire them in.

---

## Safety reminder

These are the same tweaks reputable tools use, kept to official and reversible
methods. Still: **create a restore point first**, and reboot when a tweak says
to. Don't run repair scans (SFC/DISM) and shut down mid-scan.


---

## Building from source

**Requirements:** Visual Studio 2022 (Community is free) with the
**"Desktop development with C++"** workload. No other dependencies — the app
uses only built-in Windows libraries.

1. Clone or download this repo.
2. Open **`GamingToolkit.sln`** in Visual Studio 2022.
3. Set the toolbar dropdowns to **Release** and **x64**.
4. **Build → Build Solution** (`Ctrl+Shift+B`).
5. The result is `x64\Release\GamingToolkit.exe` — a single self-contained
   executable (the C++ runtime is statically linked, so it runs on any
   Windows 10/11 machine with nothing else to install).

To run from Visual Studio, launch VS **as administrator** so the elevated
commands work without extra prompts.

---

## Contributing

Issues and pull requests are welcome. Because this tool touches system
settings, please keep changes **reversible** and avoid anything that disables
Windows Update or Microsoft Defender, or that can't be undone.

## License

MIT — see [LICENSE](LICENSE). Free for everyone, no warranty.
