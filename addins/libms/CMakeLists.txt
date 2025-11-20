cmake_minimum_required(VERSION 3.24)

set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/project/ARMCC.cmake)
include(${CMAKE_SOURCE_DIR}/project/Global.cmake)
set_toolchain()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

project(libms C)

__compiler_armcc(C)

file(GLOB_RECURSE SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c)

add_library(libms ${SOURCES})

target_include_directories(libms PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
