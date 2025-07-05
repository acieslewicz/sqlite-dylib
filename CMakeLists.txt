cmake_minimum_required(VERSION 3.20)

project(sqlite3 VERSION 3.6.10 LANGUAGES C)

add_definitions(-D_DARWIN_NO_64_BIT_INODE)

set(LIB_NAME "libsqlite3.dylib")
set(INSTALL_PATH /usr/lib)
set(VERSION 3.6.10)
set(COMPAT_VERSION 1.0.0)

set(SQLITE_SOURCES ${CMAKE_SOURCE_DIR}/sqlite3.c)

add_compile_options(
    -Wno-deprecated-non-prototype
    -Wno-incompatible-sysroot
    -Wno-expansion-to-defined
    -fno-stack-protector
    # Workardound for missing __stack_chk_*
)

add_compile_definitions(USE_MMAP NDEBUG)

# There are more idiomatic ways of setting some of these but stuff like
# target_link_libraries was not working for some reason
set(base_address 0x36422000)
add_link_options(
    ${SDK_PATH}/usr/lib/dylib1.o
    -L${SDK_PATH}/usr/lib
    -lSystem.B
    -lgcc_s.1
    -ios_version_min
    ${CMAKE_OSX_DEPLOYMENT_TARGET}
    -dead_strip
    -dead_strip_dylibs
    -dylib
    -single_module
    -prebind
    -dynamic
    -twolevel_namespace
    -twolevel_namespace_hints
    -image_base
    ${base_address}
    -install_name
    ${INSTALL_PATH}/${LIB_NAME}
)

set(arch_targets "")
# Create a target for each architecture
foreach(ARCH IN LISTS CMAKE_OSX_ARCHITECTURES)
    set(target_name libsqlite3_${ARCH})
    list(APPEND arch_targets ${target_name})
    add_library(${target_name} SHARED ${SQLITE_SOURCES})

    set_target_properties(
        ${target_name}
        PROPERTIES
            OUTPUT_NAME "${LIB_NAME}.${ARCH}"
            INSTALL_NAME_DIR "${INSTALL_PATH}"
            PREFIX ""
            SUFFIX ""
            POSITION_INDEPENDENTCODE OFF
            C_STANDARD 99
    )

    target_include_directories(
        ${target_name}
        PRIVATE ${CMAKE_SOURCE_DIR} ${SDK_PATH}/usr/include
    )

    target_compile_options(${target_name} PRIVATE -arch ${ARCH})

    target_link_options(${target_name} PRIVATE -arch ${ARCH})
endforeach()

set(lipo_inputs "")
foreach(ARCH IN LISTS CMAKE_OSX_ARCHITECTURES)
    list(APPEND lipo_inputs "${CMAKE_BINARY_DIR}/${LIB_NAME}.${ARCH}")
endforeach()

add_custom_target(
    ${LIB_NAME}
    ALL
    COMMAND
        ${LIPO} -create ${lipo_inputs} -output ${CMAKE_BINARY_DIR}/${LIB_NAME}
    DEPENDS ${arch_targets}
)
