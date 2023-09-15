# basic info
cmake_minimum_required(VERSION 3.22 FATAL_ERROR)
project(
    VMAware
    DESCRIPTION "VM detection library"
    LANGUAGES CXX
)


# compiler flags
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wpedantic")
set(CMAKE_EXE_LINKER_FLAGS "-lstdc++ -lm")


# fetch and set build type
set(available_build_types Debug Release)
if(NOT CMAKE_BUILD_TYPE)
set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build (Debug or Release)" FORCE)
endif()
if(NOT CMAKE_BUILD_TYPE IN_LIST available_build_types)
MESSAGE(FATAL_ERROR "Invalid build type: ${CMAKE_BUILD_TYPE}. Allowed values: ${available_build_types}")
endif()


# Define preprocessor macros based on the build type
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
add_compile_definitions(__DEBUG__)
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
add_compile_definitions(__RELEASE__)
endif()


# general variables
set(PROJECT_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(BUILD_DIR "${PROJECT_DIR}/build")
set(TARGET "vmaware")


# debug/release CXX flag options
if(CMAKE_BUILD_TYPE MATCHES "Debug")
    MESSAGE(STATUS "Build set to debug mode")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -DDEBUG -O0 -fsanitize=address,undefined,nullability") # -fsanitize=address,undefined,nullability
elseif(CMAKE_BUILD_TYPE MATCHES "Release")
    MESSAGE(STATUS "Build set to release mode")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -g0 -fPIE")
    if (${CMAKE_SYSTEM_PROCESSOR} MATCHES "x86_64")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native -mtune=native")
    elseif(${CMAKE_SYSTEM_PROCESSOR} MATCHES "arm")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mcpu=native")
    endif()
endif()


# add executable
add_executable(${TARGET} "src/cli.cpp")


# release stuff
if(CMAKE_BUILD_TYPE MATCHES "Release")
    install(TARGETS ${TARGET} DESTINATION bin)
endif()