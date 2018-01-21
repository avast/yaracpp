##
## CMake build script for yaracpp.
##

cmake_minimum_required(VERSION 3.6)
project(yaracpp CXX C)

# Use C++14.
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Enable all compiler warnings.
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")
endif()

# Variables.
set(YARACPP_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/include")

add_subdirectory(deps)
add_subdirectory(src)
