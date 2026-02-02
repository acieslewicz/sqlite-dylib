cmake_minimum_required(VERSION 3.20)

project(sqlite3 VERSION 3.6.10 LANGUAGES C)

add_definitions(-D_DARWIN_NO_64_BIT_INODE)

set(LIB_NAME "libsqlite3.dylib")
set(INSTALL_PATH /usr/lib)
set(VERSION 3.6.10)
set(COMPAT_VERSION 1.0.0)

set(IMAGE_BASE 0x36422000)

set(SQLITE_SOURCES ${CMAKE_SOURCE_DIR}/sqlite3.c)

add_library(${LIB_NAME} SHARED ${SQLITE_SOURCES})

set_target_properties(${LIB_NAME} PROPERTIES
    OUTPUT_NAME "sqlite.${VERSION}"
    VERSION ${VERSION}
    MACHO_COMPATIBILITY_VERSION ${COMPAT_VERSION}
    INSTALL_NAME_DIR "${INSTALL_PATH}"
    OSX_ARCHITECTURES "armv7"
    POSITION_INDEPENDENT_CODE OFF
    C_STANDARD 99
)

target_compile_options(${LIB_NAME}
    PRIVATE
        -Wno-deprecated-non-prototype
        -Wno-expansion-to-defined
        -fno-stack-protector
        -Os
)

target_compile_definitions(${LIB_NAME}
    PRIVATE
        USE_MMAP
        NDEBUG
)

target_link_options(${LIB_NAME}
    PRIVATE
        -dead_strip
        -dead_strip_dylibs
        -dylib
        -single_module
        -prebind
        -dynamic
        -twolevel_namespace
        -twolevel_namespace_hints
        -image_base
        ${IMAGE_BASE}
        -install_name
        ${INSTALL_PATH}/${LIB_NAME}
)
