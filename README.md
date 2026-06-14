# Gaming Performance Toolkit

A Windows optimization toolkit written in C++ (Win32 GUI). It bundles
performance tweaks, privacy toggles, app installers, repair tools, handy
shortcuts, and a timer-resolution toggler into one tabbed window.

> **First C++ project notes are sprinkled through the code as comments.**
> Read `main.cpp` first, then `actions.cpp`, then `timer_resolution.cpp`.

---

## 1. What you need to install

1. **Visual Studio 2022 Community** (free): https://visualstudio.microsoft.com/
2. In the installer, check the workload **"Desktop development with C++"**.
   That's the only workload you need.

That's it. This project uses **only Windows' built-in libraries** — no external
packages to download, so it builds out of the box.

---

## 2. How to open and build

1. Unzip this folder somewhere (e.g. `C:\Projects\GamingToolkit`).
2. Double-click **`GamingToolkit.sln`** — it opens in Visual Studio.
3. At the top, set the dropdowns to **Release** and **x64**.
4. Menu: **Build → Build Solution** (or press `Ctrl+Shift+B`).
5. The compiled program appears in `x64\Release\GamingToolkit.exe`.

### Running it
- The app **requires administrator rights** (the tweaks need them). Because of
  the linker's UAC setting, Windows will automatically show the UAC prompt when you
  launch the .exe — just click **Yes**.
- In Visual Studio, debugging (`F5`) also works, but VS itself must be running
  as admin for the elevated commands to succeed without extra prompts.

---

## 3. What each tab does

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
| **Tweaks** | Scrollable, tooltipped checkbox list of 90+ tweaks across Performance/Latency, Privacy/Telemetry, Debloat, Network/Gaming, and Appearance |
| **Drivers** | Windows Update, optional updates, Device Manager, NVIDIA/AMD official pages, Intel Driver Assistant, get DDU, DDU Safe-Mode guide |
| **BIOS & Hardware** | Detects CPU/GPU/RAM, checks whether XMP/EXPO looks enabled (rated vs running RAM speed), XMP/EXPO guide, reboot-to-BIOS |
| **System Info** | Full read-only specs report (OS, board, BIOS, CPU clocks, RAM sticks/speeds, GPU+driver+resolution, storage, network); copy to clipboard; open DxDiag |
| **About** | By peeweegecko — for the people |

The **Shortcuts** tab also includes **Restart Explorer** (restarts the
taskbar/desktop `explorer.exe` process).

A **live CPU / RAM monitor** runs in the bottom-right of the window, updating
once a second.

---

## 4. How it works (the important idea)

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

## 5. New in this version

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

## 6. Notes, limits, and easy next steps

- **DNS**: falls back to "Wi-Fi"/"Ethernet" only if detection finds nothing.
- **Ideas to grow this** (good learning exercises):
  - Make *Save Profile* read live checkbox states instead of a fixed set
  - Add GPU usage/temperature (needs a vendor API or WMI)
  - An FPS overlay (bigger project — needs DirectX/overlay hooking)
  - Upgrade the UI to **Dear ImGui** for a slicker look

Ask me for any of these and I'll wire them in.

---

## 7. Safety reminder

These are the same tweaks reputable tools use, kept to official and reversible
methods. Still: **create a restore point first**, and reboot when a tweak says
to. Don't run repair scans (SFC/DISM) and shut down mid-scan.
