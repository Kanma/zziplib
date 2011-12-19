cmake_minimum_required(VERSION 2.6)
project(ZZIPLIB)


##########################################################################################
# XMake-related settings

# Set the output paths
if (NOT DEFINED XMAKE_BINARY_DIR)
    set(XMAKE_BINARY_DIR "${ZZIPLIB_BINARY_DIR}")
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/lib")
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${XMAKE_BINARY_DIR}/bin")
endif()

# Set the dependencies path
if (NOT DEFINED XMAKE_DEPENDENCIES_DIR)
    set(XMAKE_DEPENDENCIES_DIR "${ZZIPLIB_SOURCE_DIR}/dependencies")
endif()


##########################################################################################
# XMake importation

if (NOT EXISTS "${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")
    message(FATAL_ERROR
"Missing dependency: XMake
This distribution of zziplib requires the following dependencies:
   - XMake
   - ZLib
They are provided as GIT submodules of this repository.
Did you forgot to execute the following commands?
   git submodule init
   git submodule update")
endif()

include("${XMAKE_DEPENDENCIES_DIR}/XMake/XMake.cmake")


##########################################################################################
# Process subdirectories

add_subdirectory(dependencies)


##########################################################################################
# Declaration of the library

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
set(INCLUDE_PATHS "${ZZIPLIB_SOURCE_DIR}")

include_directories(${INCLUDE_PATHS})

xmake_import_search_paths(ZLIB)

# Declaration of the library
xmake_create_static_library(ZZIPLIB zziplib ${SRCS})

if (NOT WIN32)
    xmake_add_to_property(ZZIPLIB COMPILE_FLAGS "-w")
else()
    xmake_add_to_property(ZZIPLIB COMPILE_FLAGS "/W0")
    xmake_add_to_list_property(ZZIPLIB COMPILE_DEFINITIONS "_CRT_SECURE_NO_DEPRECATE")
endif()

xmake_project_link(ZZIPLIB ZLIB)

# Exportations
xmake_export_include_paths(ZZIPLIB ${INCLUDE_PATHS})
