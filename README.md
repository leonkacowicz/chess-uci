# What is this?
This package is a one of a series packages intended to support the 
development of general purpose [chess engines and tools](https://github.com/leonkacowicz/chess-tools).

This package implements a library for communicating between a chess front-end/arbiter 
and a chess-engine using the [UCI protocol](https://chessprogramming.org/UCI).

# How do I build it?

This package uses CMake as a build-tool. In theory, it requires CMake 3.5,
but I haven't tested it with previous versions of CMake. If you manage to
build it with an older version of CMake, let me know or send a pull request
decreasing the required version on line 1 of `/CMakeLists.txt`

To build it:
```sh
mkdir build
cd build
cmake ..
make
```

# Testing

After building, an executable at `build/test/uci/test_chess_uci` should be generated.
All tests specified in `/tests/` should be invoked by this executable.

# How do I import it in my CMake project?

You can add a file called `chess-uci.cmake` to your project with the following contents:

```cmake
include(ExternalProject)
ExternalProject_Add(
        chess-uci-external
        URL https://github.com/leonkacowicz/chess-uci/archive/refs/heads/main.zip
        PREFIX ${CMAKE_BINARY_DIR}/chess-uci-external
        INSTALL_COMMAND ""
        LOG_DOWNLOAD ON
        LOG_CONFIGURE ON)

add_library(libchess-uci IMPORTED STATIC GLOBAL)
add_dependencies(libchess-uci chess-uci-external)
include_directories("${CMAKE_BINARY_DIR}/chess-uci-external/src/chess-uci-external/src/uci/include")
set_target_properties(libchess-uci PROPERTIES
        INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_BINARY_DIR}/chess-uci-external/src/chess-uci-external/"
        IMPORTED_LOCATION "${CMAKE_BINARY_DIR}/chess-uci-external/src/chess-uci-external-build/src/uci/libchess_uci.a"
        )
```

Then add the following to your CMakeLists.txt:
```cmake
include(chess-uci.cmake)
target_link_libraries(YOUR_TARGET libchess-uci)
```