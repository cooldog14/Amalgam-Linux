<div align="center">

  # Amalgam-Linux
  ## <img src=".github/assets/tux.svg" alt="tux" height="100">

  Amalgam fork made to run on Linux / Proton. Insecure dialog bypass included.
  [Injecting guide](docs/INJECTING.md)

  [![Download](.github/assets/download.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64Release.zip)
  [![PDB](.github/assets/pdb.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleasePDB.zip)
  [![Download AVX2](.github/assets/download_avx2.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseAVX2.zip)
  [![PDB AVX2](.github/assets/pdb.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseAVX2PDB.zip)
  <br>
  [![Freetype](.github/assets/freetype.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseFreetype.zip)
  [![PDB Freetype](.github/assets/pdb.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseFreetypePDB.zip)
  [![Freetype AVX2](.github/assets/freetype_avx2.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseFreetypeAVX2.zip)
  [![PDB Freetype AVX2](.github/assets/pdb.svg)](https://nightly.link/cooldog14/Amalgam-Linux/workflows/msbuild/master/Amalgamx64ReleaseFreetypeAVX2PDB.zip)
</div>

## Fixes in this fork
- TF2 Oct 2026 update: `S_StartSound` signature + `cbrush_t` struct
- Accept `Team Fortress 2 - OpenGL - 64 Bit` window (Proton wined3d fallback)
- Insecure dialog bypass to join VAC-secured servers

## Quick start
1. Steam > TF2 > Compatibility > Force Proton, launch once to main menu.
2. Put injector + `Amalgamx64Release.dll` in `steamapps/compatdata/440/pfx/drive_c/`.
3. Launch TF2 to main menu, then inject:
   `protontricks -c 'wine cmd /c C:\inject.bat' 440`
4. In-game enable insecure bypass.

## Issues
Make an issue [here](https://github.com/cooldog14/Amalgam-Linux/issues).
