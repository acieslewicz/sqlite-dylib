# SQLite3 Dynamic Library Builder

A CMake-based build configuration for creating SQLite3 dynamic libraries.

## Prerequisites

Download the common-3.0 sdk from here: [common-3.0.sdk](https://github.com/touchHLE/common-3.0-sdk)

## Build Dylib

`cmake -DCMAKE_TOOLCHAIN_FILE=/path/to/common-3.0.sdk/cmake/Toolchain/common-3.0.cmake -S . -B build`

`cmake --build build`

## Version info

This build uses the SQLite 3.6.10 amalgamation as a source.

## Licensing 

Build configuration files are licensed under the Mozilla Public License 2.0. Full license text in `LICENSE`.

SQLite3 source code is in the public domain. See [SQLite Copyright](https://www.sqlite.org/copyright.html) for details.
