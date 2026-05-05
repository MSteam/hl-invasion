# README

## About The Project

This project uses the Half-Life 1 SDK and incorporates code from https://github.com/jlecorre/hlinvasion. The code has been modified to work with newer and modern compilers, while preserving the original structure of the default SDK.

## Supported Platforms

- Windows
- Linux (added in this fork)

## Built with

- [CMake](https://cmake.org/) (Windows)
- GNU make + GCC `-m32` (Linux)
- [Half-Life 1 SDK](https://github.com/ValveSoftware/halflife)
- [Visual Studio](https://visualstudio.microsoft.com/) (Windows)

## What's new in this fork

This fork adds a Linux build of the mod (server `hl.so` and client `client.so`) and several portability/runtime fixes that were needed to make it build on a modern GCC and run cleanly on Linux:

- Linux makefiles in `linux/` (adapted from the Half-Life SDK), updated for the Invasion source tree (added all Invasion-specific modules, dropped weapons removed in Invasion).
- `dlls/extdll.h`, `common/const.h`: `cstring`, GCC 15 / C23 `qboolean`, `min`/`max` macros gated to C only so they stop colliding with `std::min`/`std::max` in C++ code.
- `dlls/music.h`: `fmod.h` / `windows.h` gated to `_WIN32`; `FSOUND_STREAM` typedef'd for the Linux side so `music.cpp` still compiles.
- `dlls/genericmonster.cpp`, `dlls/sniper.cpp`, `dlls/tank.cpp`, `dlls/rpggrunt.cpp`: a few `GetAttachment(..., Vector(0,0,0))` calls reworked to use lvalue dummies (modern GCC rejects binding non-`const` references to rvalues).
- `dlls/radiomsg.cpp`, `cl_dll/vgui_OrdiMenu.cpp`, `cl_dll/vgui_SchemeManager.cpp`: hardcoded paths switched from `"foo\\bar"` to `"foo/bar"` (works on both platforms; the previous form failed on Linux's case-sensitive POSIX `fopen`).
- `cl_dll/inputw32.cpp`: was already calling `SDL_SetRelativeMouseMode` correctly — no fix needed in this fork. (Note: vanilla SDK clients on Linux often need this added to keep the cursor captured; see e.g. `halflife-master`.)
- `cl_dll/health.cpp`, `cl_dll/hudrpg.cpp`: replaced `pfnDrawString` / `pfnDrawStringReverse` based digit drawing (engine console font, ~2× larger on Linux Steam HL) with the `sprites/320hud2.spr` 12×16 digit atlas via a new `CHud::DrawSmallNumberRight` helper. This makes health, armor count, medkit count and RPG ammo readouts compact and visually consistent across platforms.
- `dlls/mod_config.h`, `cl_dll/mod_config.h`: simple stand-ins for the file CMake otherwise generates from `cmake/mod_config.h.in`, so the make-based Linux build doesn't need CMake.

## Installation

See [installation instructions](INSTALL.md).

## Building The Code

See [building instructions](BUILDING.md).

## Custom Console Commands

|Command|Description|
|:--|:--|
|modinfo|Display the mod description and version|

## Contributing

See [contribution guidelines](CONTRIBUTING.md).

## License

See [license](LICENSE.md).

## Acknowledgments

Thanks to the following resources for templates, guides and or general structural inspiration.

- [Apache Licence v2.0](https://www.apache.org/licenses/LICENSE-2.0)
- [Best README Template](https://github.com/othneildrew/Best-README-Template)
- [Half-Life: Invasion team](http://hlinvasion.free.fr/index_eng.htm)
- [Keep a Changelog](https://keepachangelog.com/)
