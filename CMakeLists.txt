cmake_minimum_required(VERSION 3.10)

project(OpenXR-Hpp-BasicExample
        LANGUAGES CXX
        VERSION 0.0.1)

# Defines the CMAKE_INSTALL_LIBDIR, CMAKE_INSTALL_BINDIR and many other useful macros.
# See https://cmake.org/cmake/help/latest/module/GNUInstallDirs.html
include(GNUInstallDirs)

# Control where libraries and executables are placed during the build.
# With the following settings executables are placed in <the top level of the
# build tree>/bin and libraries/archives in <top level of the build tree>/lib.
# This is particularly useful to run ctests on libraries built on Windows
# machines: tests, which are executables, are placed in the same folders of
# dlls, which are treated as executables as well, so that they can properly
# find the libraries to run. This is a because of missing RPATH on Windows.
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_BINDIR}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR}")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_INSTALL_LIBDIR}")

# To build shared libraries in Windows, we set CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS to TRUE.
# See https://cmake.org/cmake/help/v3.4/variable/CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS.html
# See https://blog.kitware.com/create-dlls-on-windows-without-declspec-using-new-cmake-export-all-feature/
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# Under MSVC, we set CMAKE_DEBUG_POSTFIX to "d" to add a trailing "d" to library
# built in debug mode. In this Windows user can compile, build and install the
# library in both Release and Debug configuration avoiding naming clashes in the
# installation directories.
if(MSVC)
    set(CMAKE_DEBUG_POSTFIX "d")
endif()

# Build position independent code.
# Position Independent Code (PIC) is commonly used for shared libraries so that
# the same shared library code can be loaded in each program address space in a
# location where it will not overlap with any other uses of such memory.
# In particular, this option avoids problems occurring when a process wants to
# load more than one shared library at the same virtual address.
# Since shared libraries cannot predict where other shared libraries could be
# loaded, this is an unavoidable problem with the traditional shared library
# concept.
# Generating position-independent code is often the default behavior for most
# modern compilers.
# Moreover linking a static library that is not built with PIC from a shared
# library will fail on some compiler/architecture combinations.
# Further details on PIC can be found here:
# https://eli.thegreenplace.net/2011/11/03/position-independent-code-pic-in-shared-libraries/
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Disable C and C++ compiler extensions.
# C/CXX_EXTENSIONS are ON by default to allow the compilers to use extended
# variants of the C/CXX language.
# However, this could expose cross-platform bugs in user code or in the headers
# of third-party dependencies and thus it is strongly suggested to turn
# extensions off.
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_CXX_EXTENSIONS OFF)


# Encourage user to specify a build type (e.g. Release, Debug, etc.), otherwise set it to Release.
if(NOT CMAKE_CONFIGURATION_TYPES)
    if(NOT CMAKE_BUILD_TYPE)
        message(STATUS "Setting build type to 'Release' as none was specified.")
        set_property(CACHE CMAKE_BUILD_TYPE PROPERTY VALUE "Release")
    endif()
endif()


if (NOT "${CMAKE_SIZEOF_VOID_P}" EQUAL "8")
    message( FATAL_ERROR "Only 64 bit builds supported." )
endif()


### Dependencies
find_package(Threads REQUIRED)
find_package(OpenXR REQUIRED)
find_package(SDL2 CONFIG REQUIRED)

## This is to select the newest version of OpenGL
if (POLICY CMP0072)
  cmake_policy (SET CMP0072 NEW)
endif(POLICY CMP0072)

find_package(OpenGL REQUIRED)

if (NOT WIN32)
    find_package(X11 REQUIRED)
endif()

### Attempt to find OpenXR-Hpp directory
set(OpenXR-Hpp_DIR CACHE PATH "$ENV{OpenXR-Hpp_DIR}")
find_file(openxrhpp_file openxr.hpp
          HINTS ${OpenXR-Hpp_DIR}
          PATH_SUFFIXES "include/openxr" "openxr")
mark_as_advanced(openxrhpp_file)

if (${openxrhpp_file} STREQUAL "openxrhpp_file-NOTFOUND")
    message (FATAL_ERROR "openxr.hpp file not found. Set the OpenXR-Hpp_DIR environmental variable with the absolute path to the generated cpp files. See https://github.com/KhronosGroup/OpenXR-Hpp")
endif()
get_filename_component(openxrhpp_source_DIR ${openxrhpp_file} DIRECTORY)
get_filename_component(OpenXR-Hpp_INCLUDE_DIRS ${openxrhpp_source_DIR} DIRECTORY)

add_library(OpenXR-Hpp INTERFACE)
target_include_directories(OpenXR-Hpp INTERFACE ${OpenXR-Hpp_INCLUDE_DIRS})
add_library(OpenXR::OpenXR-Hpp ALIAS OpenXR-Hpp)


### Add the target
set(TARGET_NAME ${PROJECT_NAME})

add_executable(${TARGET_NAME} main.cpp)

target_link_libraries(${TARGET_NAME} PRIVATE OpenXR::openxr_loader)
target_link_libraries(${TARGET_NAME} PRIVATE OpenXR::OpenXR-Hpp)
target_link_libraries(${TARGET_NAME} PRIVATE ${SDL2_LIBRARIES})
target_link_libraries(${TARGET_NAME} PRIVATE ${OPENGL_LIBRARIES})

if (NOT WIN32)
    target_link_libraries(${TARGET_NAME} PRIVATE ${X11_LIBRARIES})
endif()


