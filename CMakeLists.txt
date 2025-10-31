cmake_minimum_required(VERSION 3.24)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/ARMCC.cmake)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(NOT DEFINED ENV{ARMCC_PATH})
    message(FATAL_ERROR "please set ARMCC_PATH")
endif()

find_program(ARM_ASM NAMES armasm.exe PATHS $ENV{ARMCC_PATH}/bin REQUIRED NO_DEFAULT_PATH)
find_program(ARM_AR NAMES armar.exe PATHS $ENV{ARMCC_PATH}/bin REQUIRED NO_DEFAULT_PATH)
find_program(ARM_CC NAMES armcc.exe PATHS $ENV{ARMCC_PATH}/bin REQUIRED NO_DEFAULT_PATH)
find_program(ARM_LINK NAMES armlink.exe PATHS $ENV{ARMCC_PATH}/bin REQUIRED NO_DEFAULT_PATH)
# find_program(ARM_FROMELF NAMES fromelf.exe PATHS $ENV{ARMCC_PATH}/bin REQUIRED NO_DEFAULT_PATH) # not important

macro (set_compilers)
    set(CMAKE_CXX_COMPILER_ID ARMCC)

    set(CMAKE_AR ${ARM_AR})
    set(CMAKE_C_COMPILER ${ARM_CC})
    set(CMAKE_CXX_COMPILER ${ARM_CC})
    set(CMAKE_ASM_COMPILER ${ARM_ASM})
    set(CMAKE_LINKER ${ARM_LINK})
endmacro()

if (WIN32)
    set_compilers()
    set(CMAKE_C_COMPILER_WORKS TRUE)
    set(CMAKE_CXX_COMPILER_WORKS TRUE)
    set(CMAKE_ASM_COMPILER_WORKS TRUE)
endif()

project(sead C CXX)

if (NOT WIN32)
    set_compilers()
endif()

__compiler_armcc(C)
__compiler_armcc(CXX)

file(GLOB_RECURSE SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

add_library(sead ${SOURCES})

target_include_directories(sead PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../ctrsdk/include)
target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:--apcs=//interwork --arm --cpp>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:--cpu=MPCore --fpmode=fast --force_new_nothrow>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:--locale=japanese --multibyte-chars --signed_chars>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:--no_exceptions --no_rtti --no_rtti_data>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:-O3 -Otime --data-reorder --split_sections --forceinline>)

target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:C>:--apcs=//interwork --arm --c99>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:C>:--cpu=MPCore --fpmode=fast>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:C>:--locale=japanese --multibyte-chars --signed_chars>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:C>:-Otime --data-reorder --split_sections>)

