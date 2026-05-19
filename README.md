# IDA SDK

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![C++11](https://img.shields.io/badge/C++-17%2B-blue.svg)](https://isocpp.org/std/the-standard)  

A Software Development Kit for extending [IDA](https://hex-rays.com/ida-pro/), the state-of-the-art binary code analysis tool. The C++ SDK allows you to build custom plugins, loaders, processors, and scripts that integrate directly into IDA. This project also includes [IDAPython SDK](src/plugins/idapython/README.md) allowing you to write plugins using Python.  


## Documentation
- [IDA Documentation](https://docs.hex-rays.com/)
- [C++ Developer’s Guide](https://docs.hex-rays.com/developer-guide/c++-sdk)  
- [SDK reference](https://cpp.docs.hex-rays.com/)  
- Additional README files in subdirectories provide details for processor modules, loaders, and other components.

## Contributing

The SDK is currently closed to external contributions, as it requires tight integration with our proprietary IDA Pro workflows and follows an internal roadmap with specific architectural decisions.

However, feel free to report bugs or suggest features via [Issues](https://github.com/HexRaysSA/ida-sdk/issues).

## Versioning

Git tags use [semver](https://semver.org/)-compatible syntax to track both IDA product releases and independent SDK updates:

| Tag pattern | Meaning |
|---|---|
| `vX.Y.Z-release` | SDK snapshot shipped with an IDA release |
| `vX.Y.Z-sdk.N` | SDK-only update between IDA releases (build system, docs, examples, etc.) |

`X.Y.Z` matches the IDA version that the SDK targets. The pre-release suffix distinguishes the type of change:

- **`-release`** marks the exact SDK state shipped with a given IDA version.
- **`-sdk.N`** marks subsequent SDK improvements that still target the same IDA version. `N` increments with each update.

**Sort order.** Semver compares pre-release identifiers lexically, so `-release` sorts before `-sdk.N` (`r` < `s`), which gives the correct chronological order:

```
v9.3.0-release        ← IDA 9.3.0 ships, SDK updated to match
v9.2.0-sdk.2          ← second SDK-only update for IDA 9.2.0
v9.2.0-sdk.1          ← first SDK-only update (e.g., CMake support added)
v9.2.0-release        ← IDA 9.2.0 ships (initial commit)
```

**Note:** In strict semver, a pre-release version (e.g., `v9.3.0-release`) has lower precedence than the corresponding normal version (`v9.3.0`). Since this repository never publishes bare `vX.Y.Z` tags, the ordering among tagged versions is unambiguous and correct.

## Requirements

To build:

- **C++ compiler** that fully supports C++17 compatible with your platform:
    - GCC or LLVM/Clang (Linux/macOS)
    - Microsoft Visual C++ (MSVC) 2022 or later (Windows)
- **cmake**

To use the SDK:

- **IDA 9.2**

## Repository fundamentals 

- `src/module/` — Processor modules  
- `src/ldr/` — Loader modules  
- `src/include/` — Core IDA SDK headers  
- `src/lib/` — Stub libraries for linking against IDA kernel  
- `src/dbg/` — Debugger server integration  
- `src/bin/` — Build helpers & binaries  
- `src/idalib/` — IDA as a library examples  
- `src/plugins/` — Sample plugin modules (including IDAPython)  

## Getting Started

**CMake is the supported build system for the IDA SDK.**

To install the project on your local machine, refer to the instructions in [src/readme.txt](src/readme.txt).
Additional README files in subdirectories provide more details about:

- Processor module templates
- Loaders
- Other components

### Building

**Basic build (all components except Qt plugins):**

Linux/macOS/Windows:
```shell
cd src/
cmake -B build -G Ninja # Or other generator
cmake --build build
```

**Build with Qt6 support (optional)** (for qproject/qwindow plugins):

Qt plugins (qproject, qwindow) require Qt6 with QT_NAMESPACE=QT. The build system automatically builds Qt6 6.5.3 from source when requested:

```cmd
:: Build Qt6 once (takes 1-2 hours, only needed once per build directory)
cd src\
cmake -B build
cmake --build build --target build_qt

:: Qt is auto-detected and plugins rebuild automatically
cmake --build build --config Release
```

Linux/macOS:
```shell
cd src/
cmake -B build
cmake --build build --target build_qt  # Build Qt6 from source
cmake --build build                     # Build with Qt support
```

**How it works:**
- Qt6 is downloaded and built in `build/qt-install/` (4GB, one-time setup)
- Only essential modules are built (qtbase: Core, Gui, Widgets, OpenGL)
- Cross-platform support: Windows (MSVC), Linux (GCC/Clang), macOS (Universal)
- Qt is auto-detected on subsequent builds

**Note:** All other plugins build without Qt. Qt plugins are skipped gracefully if Qt is not found.

For detailed CMake build options and troubleshooting, see [CLAUDE.md](CLAUDE.md).

#### Debugger Servers

This SDK does not provide debugger servers for macOS.

Building Debugger Servers on Linux/Windows:
```shell
cd src/
cmake --preset dbgserver
cmake --build --preset dbgserver

cmake --preset dbgserver-ea32
cmake --build --preset dbgserver-ea32
```

### Run

Depending on your build process, some binaries in `src/bin` may run out of the box. Others may need to be moved to your IDA installation directory.

To use custom-built plugins, you must place them in a location recognized by IDA. You can do this in one of the following ways:

- **Copy plugins manually**

    Copy the contents of `src/bin/plugins/` to:

    - `~/.idapro/plugins/` on **Linux/macOS**
    - `%APPDATA%\Hex-Rays\IDA Pro\plugins\` on **Windows**

    Be careful not to overwrite existing plugins.

- **Use the `IDAUSR` environment variable**

    Set or extend the [`IDAUSR`](https://docs.hex-rays.com/user-guide/user-interface/menu-bar/windows/environment-variables) environment variable to include the `src/bin` path.

- **Install directly to the IDA directory**

    Copy the contents of `src/bin/plugins/` to `<your IDA installation>/plugins/`.
    Be cautious about overwriting existing files.

For more details, see the [Getting Started Documentation](https://docs.hex-rays.com/developer-guide/c++-sdk/c++-sdk-getting-started).

## Build Documentation

To build the documentation locally:

### Install Tools

#### macOS
```bash
brew install doxygen graphviz
```

#### Linux (Debian/Ubuntu)
```bash
sudo apt install doxygen graphviz
```

#### Windows
- Download Doxygen installer from [doxygen.nl](https://www.doxygen.nl/download.html).
- Install Graphviz separately and add it to PATH.

---

### Building Documentation

From docs folder:
```bash
doxygen Doxyfile
```

Output will appear in:
- `docs/build/` 

### Online Documentation

The latest documentation is available at: [https://cpp.docs.hex-rays.com/](https://cpp.docs.hex-rays.com/)


## Related Projects

### Python SDK

The following previous IDAPython repositories have now been archived and moved into this project [IDAPython](src/plugins/idapython/README.md):

- ~~https://github.com/idapython/src~~
- ~~https://github.com/HexRaysSA/IDAPython~~

For more information, refer to the [IDAPython documentation](https://docs.hex-rays.com/developer-guide/idapython)

## Support

For commercial support or licensing inquiries, contact: support@hex-rays.com

For community help, visit:
[IDA Users Forum](https://community.hex-rays.com/c/idas/api-sdk/15)


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
