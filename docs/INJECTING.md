# Injecting Amalgam on Linux / Proton

This replaces the upstream [linuxgamer wiki](https://github.com/linuxgamer/Amalgam-Linux/wiki),
which is stale: wrong prefix path, Linux paths in the `.bat`, no second-library
case, no OpenGL / signature fixes.

## 1. Prerequisites

1. Steam > TF2 > Properties > Compatibility > **Force the use of a specific
   Steam Play compatibility tool** (GE-Proton or Proton Experimental).
2. Launch TF2 once to the main menu, then quit. This creates the real prefix.
3. Find the real prefix — TF2 is often on a second library:
   ```
   find ~ /mnt -maxdepth 8 -type d -path "*compatdata/440/pfx" 2>/dev/null
   ```
   Example: `/mnt/hdd/games/steam/steamlibrary/SteamLibrary/steamapps/compatdata/440/pfx`
4. If you ever made a fake `~/.local/share/Steam/steamapps/compatdata/440` by hand,
   delete it. It causes `Multiple compatdata directories found for app 440`
   and `protontricks-launch --appid 440` will look in the wrong place.
5. Verify: `protontricks -s "Team Fortress"` must list `Team Fortress 2 (440)`.

## 2. Files

Put all three in the **same** real `drive_c/` (i.e. `.../compatdata/440/pfx/drive_c/`):

- `DLL_Injector-x64-Release.exe` ([linuxgamer/DLL-Injector](https://github.com/linuxgamer/DLL-Injector), x64 Release)
- `Amalgamx64Release.dll` (from this fork's nightly link in the README)
- `inject.bat`

`inject.bat` must use **Windows** paths (`C:\...`), not Linux paths:
```bat
@echo off
setlocal
set GAME_EXE=tf_win64.exe
set INJECTOR_PATH=C:\DLL_Injector-x64-Release.exe
set DLL_PATH=C:\Amalgamx64Release.dll
"%INJECTOR_PATH%" "%DLL_PATH%" "%GAME_EXE%"
pause
```

## 3. Launch options

Do **not** use `-gl`. It makes the window title
`Team Fortress 2 - OpenGL - 64 Bit`, which Amalgam rejects
(`Amalgam/src/SDK/SDK.cpp` only accepts Direct3D 9 / Vulkan).
That gives `Failed to load in time ... 0x0` after 60s.

Steam > TF2 > Properties > Launch Options:
```
-novid -nojoy -nosteamcontroller -nohltv -particles 1 -vulkan -windowed -noborder
```

## 4. Inject

1. Launch TF2, wait until fully at the main menu.
2. In a Linux terminal:
   ```
   protontricks -c 'wine cmd /c C:\inject.bat' 440
   ```
   Alternative with a Linux path:
   ```
   protontricks-launch --appid 440 "/path/to/compatdata/440/pfx/drive_c/inject.bat"
   ```
   Note: `protontricks-launch --appid 440 cmd ...` does **not** work —
   it tries to resolve `cmd` as a Linux path. Use `protontricks -c 'wine ...'`
   for builtins like `cmd`.
3. Optional helper `tf2-inject.sh` used during debugging: finds the TF2 window
   with `xdotool`, renames it to an accepted title, then runs the command above.
4. Back in TF2, open the menu (Insert) and enable insecure dialog bypass.

The `pressure-vessel-wrap ... Not sharing path` lines and `fixme:hid` lines
are harmless Proton noise.

## 5. Fixes in this fork vs upstream

- `S_StartSound` signature, TF2 Oct 2026 update (`engine.dll`
  `40 53 ...` -> `40 57 48 81 EC ? ? ? ? 48 83 79`, upstream `d063a96`).
  Stale DLL fails with `CSignature::Initialize() failed to initialize: S_StartSound`.
- `cbrush_t` struct (`unsigned short numsides/firstbrushside`,
  `0xFFFF` -> `int` / `0xFFFFFFFF`, upstream `d063a96`).
  Stale DLL loads at menu but crashes with `ACCESS VIOLATION (0xC0000005)`
  when joining a map.
- Accept `Team Fortress 2 - OpenGL - 64 Bit` window title (Proton wined3d
  fallback), plus the `tf2-inject.sh` rename workaround.
- Insecure dialog bypass to join VAC-secured servers (from linuxgamer).

## 6. Logs

- `.../Team Fortress 2/Amalgam/fail_log.txt`
  - `... (sig) / 0x0` = window not found (wrong title, injected too early).
  - `CSignature::Initialize() failed ... S_StartSound` = DLL predates TF2 update.
- `.../Team Fortress 2/Amalgam/crash_log.txt`
  - `ACCESS VIOLATION` on join = stale `cbrush_t` struct, needs rebuilt DLL.

## 7. Rebuilding

```
gh repo fork linuxgamer/Amalgam-Linux --clone=false
git clone https://github.com/<you>/Amalgam-Linux
# apply fix patch, commit, push to master
```

Push triggers `.github/workflows/msbuild.yml` (`windows-latest`, v143, 4 configs).
Download `Amalgamx64Release.zip` via the nightly link in the README,
replace `C:\Amalgamx64Release.dll`, restart TF2 fully before re-injecting.
