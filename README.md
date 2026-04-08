# Supermodel: A Sega Model 3 Arcade Emulator
*Copyright 2003-2024 The Supermodel Team*

## Overview

[Supermodel](https://supermodel3.com) emulates Sega's Model 3 arcade platform, allowing you to relive state-of-the-art 3D arcade gaming as it existed from 1996 through 1999. It uses OpenGL, [SDL2](https://libsdl.org), and can run on Windows, Linux, and macOS. It also supports network play on low-latency network connections. The source code is freely available under the terms of the [GNU General Public License](http://www.gnu.org/copyleft/gpl.html).

<p align="center">
  <img src="Docs/Images/Daytona2_1.gif" width="375" height="250" /> <img src="Docs/Images/LAMachin.gif" width="375" height="250" />
  <img src="Docs/Images/StarWars.gif" width="375" height="250" /> <img src="Docs/Images/FVipers2.gif" width="375" height="250" />
</p>

<img src="Docs/Images/Real3D_Logo.png" align="right" height="100"> Model 3 first made its debut in 1996 with Virtua Fighter 3 and Scud Race, and for the subsequent three years boasted the most powerful 3D hardware of any gaming platform. Developed by Real3D, then a Lockheed Martin company, and with a heritage rooted in advanced flight simulator technology, Model 3 featured capabilities that would not appear on PCs for several years. Using an on-board scene graph and geometry processor, it could store, transform, light, and rasterize tens of thousands of polygons per frame at a fluid 57.524 frames per second.

The aim of the Supermodel project is to develop an emulator that is both accurate and playable. As with virtually all arcade hardware, no public documentation for the Model 3 platform exists. What is known so far has been painstakingly reverse engineered from scratch.

## How To Get It

Windows builds are updated automatically and available on the official Supermodel [download page](https://supermodel3.com/Download.html). Linux and macOS users currently have to build from source.

## Build Instructions

### Windows

The preferred method for building Supermodel is to use GCC and MSYS2. After installing [MSYS2](https://msys2.org), open the MSYS2 shell and install the required dependencies using the pacman package manager:

- GCC (```mingw64/mingw-w64-x86_64-gcc```)
- Make (```mingw64/mingw-w64-x86_64-make```)
- SDL2 (```mingw64/mingw-w64-x86_64-SDL2```, ```mingw64/mingw-w64-x86_64-SDL2_net```)

This can be done using the following commands:

```
pacman -S mingw64/mingw-w64-x86_64-gcc
pacman -S mingw64/mingw-w64-x86_64-make
pacman -S mingw64/mingw-w64-x86_64-SDL2
pacman -S mingw64/mingw-w64-x86_64-SDL2_net
```

At this point, you can continue using either the MSYS2 shell or Windows Command Prompt but ensure that both ```gcc``` and ```mingw32-make``` are in your path. In MSYS2, the location of these binaries will be ```/mingw64/bin``` and for Command Prompt, assuming MSYS2 was installed in the default location, add ```C:\msys64\mingw64\bin``` to your Windows ```PATH``` variable.

To build Supermodel without network support, use:

```
mingw32-make -f Makefiles/Makefile.Win32
```

For network support:

```
mingw32-make -f Makefiles/Makefile.Win32 NET_BOARD=1
```

### Linux

Ensure SDL2 is installed. Most package managers ought to have this available. For example, on Ubuntu, it should be sufficient to run:

```
sudo apt install libsdl2-dev
sudo apt install libsdl2-net-dev
sudo apt install libglu1-mesa-dev
```

And then build Supermodel:

```
make -f Makefiles/Makefile.UNIX
```

For network support:

```
make -f Makefiles/Makefile.UNIX NET_BOARD=1
```

### macOS

Ensure Apple's Xcode Command Line Tools are installed:

From a terminal:
```
xcode-select --install
```

Ensure SDL2 is installed.  Download the latest *.dmg files from both of the links below, and install per the READMEs in the .dmgs (i.e. in "/Library/Frameworks")

* SDL2: https://github.com/libsdl-org/SDL/releases

* SDL_net: https://github.com/libsdl-org/SDL_net/releases


And then build Supermodel:

```
make -f Makefiles/Makefile.OSX
```

For network support:

```
make -f Makefiles/Makefile.OSX NET_BOARD=1
```

## Libretro Builds

The libretro core is built separately from the standalone emulator binaries.
This repository includes convenience scripts for the most common targets:

- `build32.bat` for a Win32 libretro DLL
- `build64.bat` for a Win64 libretro DLL
- `buildlinux.bat` for a Linux libretro `.so`

### What you need for RetroArch

To use Supermodel as a RetroArch core, you need more than just the binary:

- the libretro core itself:
  - `supermodel_libretro.dll` on Windows
  - `supermodel_libretro.so` on Linux
- the matching RetroArch core info file:
  - `supermodel_libretro.info`
- Supermodel's game definition XML:
  - `Games.xml`
- Supermodel's optional custom music XML:
  - `Music.xml`

The XML files are important because Supermodel's libretro core still uses the
same Model 3 game definitions and custom MPEG track mappings as the standalone
emulator. `Games.xml` is required for game detection and proper ROM set
loading; `Music.xml` is used for titles that support custom MPEG music tracks.

Recommended RetroArch layout:

- Core binary:
  - Windows: `RetroArch/cores/supermodel_libretro.dll`
  - Linux: `RetroArch/cores/supermodel_libretro.so`
- Core info:
  - `RetroArch/info/supermodel_libretro.info`
- Game XML files:
  - `RetroArch/system/Supermodel/Config/Games.xml`
  - `RetroArch/system/Supermodel/Config/Music.xml`

If you prefer a different RetroArch system directory, keep the same relative
layout under that directory:

```
system/
  Supermodel/
    Config/
      Games.xml
      Music.xml
    NVRAM/
```

The libretro core will look for `Games.xml` and `Music.xml` in the RetroArch
system directory, under `Supermodel/Config/`.

### Windows

The Windows scripts expect MSYS2 to be installed in `C:\msys64`.
They build the libretro core with the matching MinGW toolchain and place the
resulting binaries in:

- `lib32/supermodel_libretro.dll`
- `lib64/supermodel_libretro.dll`

Run the scripts from a regular Command Prompt:

```bat
build32.bat
build64.bat
```

### Linux

`buildlinux.bat` stages the source tree to a remote Linux machine over SSH,
builds the libretro core there, and copies the resulting `.so` back into the
local `lib64/` folder. The script is meant to keep the remote tree persistent
so incremental rebuilds are faster.

If you are building manually on Linux, the libretro core can also be built
directly with the regular UNIX makefile and the libretro flags enabled:

```bash
make -f Makefiles/Makefile.UNIX LIBRETRO=1 BITS=64
```

That is the same build path used by the Linux `.so` release asset and by the
remote Linux build script.

### Installing the built core in RetroArch

If you build the core yourself, copy the output files into your RetroArch
installation:

1. Copy the core binary to `RetroArch/cores/`.
2. Copy `supermodel_libretro.info` to `RetroArch/info/`.
3. Copy `Games.xml` and `Music.xml` to
   `RetroArch/system/Supermodel/Config/`.
4. Optionally, create `RetroArch/system/Supermodel/NVRAM/` if you want a
   dedicated NVRAM directory outside RetroArch's normal save path.

When the core starts, RetroArch will use:

- the `info` file to identify the core and its supported extension (`zip`)
- `Games.xml` to detect the ROM set and its input profile
- `Music.xml` only for games that use custom MPEG music tracks

### RetroArch assets

For RetroArch, the core binary should be accompanied by the matching
`supermodel_libretro.info` file. The release assets published for this fork
include:

- `supermodel_libretro-win32.dll`
- `supermodel_libretro-win64.dll`
- `supermodel_libretro-linux.so`
- `supermodel_libretro.info`

The GitHub Releases page is the easiest way to grab a ready-to-use package if
you do not want to build the core yourself.

### Note: running on macOS
If you try and run a macOS binary that was downloaded from the internet and/or built on a different machine, you need to grant macOS permission to execute the binary (just 1-time):

* Open the folder containing the binary in Finder, and right (or ctrl) click on it:

* Click "Open" when the following dialogue box appears : "macOS cannot verify the developer of “supermodel-git-xxxx”. Are you sure you want to open it?"

* Close the terminal window that opens (after clicking open)

Details: https://support.apple.com/guide/mac-help/open-a-mac-app-from-an-unidentified-developer-mh40616/mac
