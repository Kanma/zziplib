cmake_minimum_required(VERSION 2.6)
project(ZZIPLIB)

# Options
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build, options are: None (CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif()

# Set the output path
if (NOT ATHENA_BINARY_DIR)
    set(ATHENA_BINARY_DIR "${ZZIPLIB_BINARY_DIR}")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${ZZIPLIB_BINARY_DIR}/lib")
endif()

# Import XMake
if (EXISTS "${ZZIPLIB_SOURCE_DIR}/dependencies/xmake/XMake.cmake")
    include("${ZZIPLIB_SOURCE_DIR}/dependencies/xmake/XMake.cmake")
endif()

# Process the dependencies
add_subdirectory(dependencies)

# XMake settings
xmake_init_static(ZZIPLIB)
xmake_add_include_paths(ZZIPLIB ${ZZIPLIB_SOURCE_DIR}
                                ${XMAKE_ZLIB_INCLUDE_PATHS})
xmake_add_link_paths(ZZIPLIB "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}")
xmake_add_link_targets(ZZIPLIB zziplib "${XMAKE_ZLIB_LINK_TARGETS}")

# List the source files
set(SRCS zzip/dir.c
         zzip/err.c
         zzip/fetch.c
         zzip/file.c
         zzip/info.c
         zzip/plugin.c
         zzip/stat.c
         zzip/zip.c
)

# List the include paths
xmake_include_directories(ZZIPLIB)

# Declaration of the library
add_library(zziplib STATIC ${SRCS})

# Compilation settings
if (NOT WIN32)
    set_target_properties(zziplib PROPERTIES COMPILE_FLAGS "-w")
else()
    set_target_properties(zziplib PROPERTIES COMPILE_FLAGS "/W0")
    set_target_properties(zziplib PROPERTIES COMPILE_DEFINITIONS "_CRT_SECURE_NO_DEPRECATE")
endif()
