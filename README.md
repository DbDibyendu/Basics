# cmake
Last edited by Yogesh Hegde 1 week ago
CMake
This guide will help you write cmake files for your project.

Cmake is used to compile your project.

Requirements to read this
Must know the basics of CMake and CMakeLists.txt
Minimum CMakeLists.txt
Bare minimum CMakelists.txt for compiling

Binaries/Applications
```shell
# Minimum Cmake verison required
cmake_minimum_required(VERSION 3.0.0)

# Project name 
project(project-name-here VERSION 0.1.0)

# Global Install Directories
include(GNUInstallDirs)

# ----------------------------------
#    Section for compiling 1 binary 
# ----------------------------------
# Compile for one binary

# List all the files for compiling one binary
set(BIN_SRC_FILES
    src/main.cpp)

add_executable(binary_name ${BIN_SRC_FILES})

# Define Compiler Flags to your library
target_compile_options(binary_name PRIVATE
    $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>:
    -Wall -Wextra -Wpedantic>)

# Define the Include directories 
# <BUILD_INTERFACE> for those directories that are needed for compilation.
target_include_directories(binary_name PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>)

# Link to ShunyaInterfaces
target_link_libraries(binary_name PUBLIC siCore siYaml siPeripheral siConn siCloud siIiot siDash siAlerts)
# If you have any more external libraries to link then link here 
target_link_libraries(binary_name PUBLIC -lm )
# ----------------------------------
#    End Section for binary
# ----------------------------------
# If you have multiple binaries in a folder 
# the add more of these sections and change binary_name
```
2.Shared Libraries (.so files)
```shell
# Set the CMake minimum version
cmake_minimum_required(VERSION 3.5)

project(shunyaCamera VERSION 1.1.1)

# Find your dependencies for the project
find_package( OpenCV REQUIRED )

# Global Install Directories
include(GNUInstallDirs)

# List all your .cpp / .c files that needs to be compiled
set(SRC_FILES
    src/video.cpp
    src/parse_json.cpp
    src/roi.cpp)

add_library(${PROJECT_NAME} SHARED ${SRC_FILES})

# Add Version to your `.so` file
set_target_properties(${PROJECT_NAME} PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION 1)

# Define Compiler Flags to your library
target_compile_options(${PROJECT_NAME} PRIVATE
    $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>:
    -Wall -Wextra -Wpedantic>)

# Define the Include directories 
# <BUILD_INTERFACE> for those directories that are needed for compilation.
# <INSTALL_INTERFACE> for your project's include directories
target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<BUILD_INTERFACE:${OpenCV_INCLUDE_DIRS}>
    $<INSTALL_INTERFACE:include>)

# Link your external libraries here
target_link_libraries(${PROJECT_NAME}  PUBLIC  ${OpenCV_LIBS})

# 'make install' to the correct locations (provided by GNUInstallDirs).
install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}Config
    ARCHIVE  DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY  DESTINATION ${CMAKE_INSTALL_LIBDIR})

# This makes the project importable from the install directory
install(EXPORT ${PROJECT_NAME}Config DESTINATION share/${PROJECT_NAME}/cmake)

# Also build examples
add_subdirectory(examples)
```
