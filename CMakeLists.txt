cmake_minimum_required(VERSION 3.24)

set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/project/ARMCC.cmake)
include(${CMAKE_SOURCE_DIR}/project/Toolchain.cmake)
set_toolchain()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_subdirectory(../ctrsdk ../../build)

project(sead C CXX)

__compiler_armcc(C)
__compiler_armcc(CXX)

file(GLOB_RECURSE SOURCES_MOD ${CMAKE_CURRENT_SOURCE_DIR}/modules/src/*.c ${CMAKE_CURRENT_SOURCE_DIR}/modules/src/*.cpp)
file(GLOB_RECURSE SOURCES_ADD ${CMAKE_CURRENT_SOURCE_DIR}/addins/*/src/*.c ${CMAKE_CURRENT_SOURCE_DIR}/addins/*/src/*.cpp)

add_library(sead ${SOURCES_MOD} ${SOURCES_ADD})

target_link_libraries(sead PUBLIC ctrsdk)

target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../ctrsdk/include)
target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/addins/libms/include)
target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/modules/include)

target_compile_definitions(sead PUBLIC SEAD_COMPILEOPT_USE_NW4C)
