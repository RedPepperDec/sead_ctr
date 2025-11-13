cmake_minimum_required(VERSION 3.24)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/ARMCC.cmake)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

project(sead C CXX)

if (NOT WIN32)
    set_compilers()
endif()

if (WIN32)
    set(LOCALE_FLAG --locale=japanese)
else()
    set(LOCALE_FLAG --locale=ja_JP.SJIS)
endif()

__compiler_armcc(C)
__compiler_armcc(CXX)

file(GLOB_RECURSE SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

add_library(sead ${SOURCES})

target_include_directories(sead PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../ctrsdk/include)
target_include_directories(sead PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_compile_options(sead PUBLIC
    --apcs=//interwork --arm
    --cpu=MPCore --fpmode=fast -Otime
    --data-reorder --split_sections
    --multibyte-chars --signed_chars
    ${LOCALE_FLAG}
)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:C>:--c99>)
target_compile_options(sead PUBLIC $<$<COMPILE_LANGUAGE:CXX>:
    --cpp --force_new_nothrow --no_vfe
    --no_exceptions --no_rtti --no_rtti_data
    -O3 --forceinline
>)

