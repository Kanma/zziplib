cmake_minimum_required(VERSION 2.6)
project(ZZIPLIB)

# Options
if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build, options are: None (CMAKE_CXX_FLAGS or CMAKE_C_FLAGS used) Debug Release RelWithDebInfo MinSizeRel." FORCE)
endif()

if (NOT ZZIPLIB_ZLIB_PATH)
    set(ZZIPLIB_ZLIB_PATH "" CACHE STRING "Path to the zlib source files (by default: ${ZZIPLIB_SOURCE_DIR}/zlib)")
endif()

# Set the output path
if (NOT ATHENA_BINARY_DIR)
    set(ATHENA_BINARY_DIR "${ZZIPLIB_BINARY_DIR}")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${ZZIPLIB_BINARY_DIR}/lib")
endif()

# Subdirectories to process
if (NOT ZZIPLIB_ZLIB_PATH)
    add_subdirectory(zlib)
endif()

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
include_directories(.)

if (NOT ZZIPLIB_ZLIB_PATH)
    include_directories(zlib)
else()
    include_directories(${ZZIPLIB_ZLIB_PATH})
endif()

# Declaration of the library
add_library(zziplib STATIC ${SRCS})

# Compilation settings
if (NOT WIN32)
    set_target_properties(zziplib PROPERTIES COMPILE_FLAGS "-w")
else()
    set_target_properties(zziplib PROPERTIES COMPILE_FLAGS "/W0")
    set_target_properties(zziplib PROPERTIES COMPILE_DEFINITIONS "_CRT_SECURE_NO_DEPRECATE")
endif()
