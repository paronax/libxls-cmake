cmake_minimum_required(VERSION 3.0)
project(libxls-cmake LANGUAGES C CXX)

#----------------------------------------------------------------------------------------
# Meta
#----------------------------------------------------------------------------------------
enable_language(C CXX)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#----------------------------------------------------------------------------------------
# Sources declaration
#----------------------------------------------------------------------------------------
set(LIBXLS_PATH "libxls")
set(LIBXLS_C_SRC_PATH "libxls/src")
set(LIBXLS_C_INCLUDE_PATH "${LIBXLS_PATH}/include")
set(LIBXLS_CXX_PATH "${LIBXLS_PATH}/cplusplus")

# C & Cxx sources
set(C_LIB_SOURCES
        ${LIBXLS_C_SRC_PATH}/endian.c
        ${LIBXLS_C_SRC_PATH}/locale.c
        ${LIBXLS_C_SRC_PATH}/ole.c
        ${LIBXLS_C_SRC_PATH}/xls.c
        ${LIBXLS_C_SRC_PATH}/xlstool.c)
set(C_LIB_HEADERS
        ${LIBXLS_C_INCLUDE_PATH}/libxls/brdb.c.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/brdb.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/endian.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/locale.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/ole.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/xlsstruct.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/xlstool.h
        ${LIBXLS_C_INCLUDE_PATH}/libxls/xlstypes.h
        ${LIBXLS_C_INCLUDE_PATH}/xls.h)
set(CXX_LIB_SOURCES
        ${LIBXLS_CXX_PATH}/XlsReader.cpp)
set(CXX_LIB_HEADERS
        ${LIBXLS_CXX_PATH}/XlsReader.h)

#----------------------------------------------------------------------------------------
# Flags
#----------------------------------------------------------------------------------------
if(NOT CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    set(CMAKE_C_FLAGS "-Wall -Wextra -Wstrict-prototypes -Wno-unused-parameter -pedantic-errors")
    set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wstrict-prototypes -Wno-unused-parameter -pedantic-errors")
endif()

#----------------------------------------------------------------------------------------
# Checks & config generation
#----------------------------------------------------------------------------------------
include(CheckIncludeFile)
include(CheckIncludeFiles)
include(CheckFunctionExists)
check_include_file("memory.h" HAVE_MEMORY_H)
check_include_file("dlfcn.h" HAVE_DLFCN_H)
check_include_file("inttypes.h" HAVE_INTTYPES_H)
check_include_file("fcntl.h" HAVE_FCNTL_H)
check_include_file("memory.h" HAVE_MEMORY_H)
check_include_file("stdint.h" HAVE_STDINT_H)
check_include_file("stdlib.h" HAVE_STDLIB_H)
check_include_file("strings.h" HAVE_STRINGS_H)
check_include_file("string.h" HAVE_STRING_H)
check_include_file("wchar.h" HAVE_WCHAR_H)
check_include_file("sys/stat.h" HAVE_SYS_STAT_H)
check_include_file("sys/types.h" HAVE_SYS_TYPES_H)
check_include_file("unistd.h" HAVE_UNISTD_H)
check_include_files("stdlib.h;stdarg.h;string.h;float.h" STDC_HEADERS)
check_function_exists("iconv" HAVE_ICONV)
check_function_exists("realloc" HAVE_REALLOC)
check_function_exists("malloc" HAVE_MALLOC)
check_function_exists("strdup" HAVE_STRDUP)

configure_file(config.h.in ${PROJECT_BINARY_DIR}/config.h)

#----------------------------------------------------------------------------------------
# Targets
#----------------------------------------------------------------------------------------
add_library(libxlsreader STATIC ${C_LIB_SOURCES})
target_include_directories(libxlsreader PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH})

add_library(libxlsreader_cxx STATIC ${CXX_LIB_SOURCES})
target_include_directories(libxlsreader_cxx PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH} ${LIBXLS_CXX_PATH})
target_link_libraries(libxlsreader_cxx libxlsreader)

add_executable(xls_cxx ${LIBXLS_CXX_PATH}/main.cpp)
target_include_directories(xls_cxx PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH} ${LIBXLS_CXX_PATH})
target_link_libraries(xls_cxx libxlsreader_cxx libxlsreader)

add_executable(xls2csv ${LIBXLS_C_SRC_PATH}/xls2csv.c)
target_include_directories(xls2csv PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH})
target_link_libraries(xls2csv libxlsreader)

#----------------------------------------------------------------------------------------
# Tests
#----------------------------------------------------------------------------------------
enable_testing()

add_executable(test1 ${LIBXLS_PATH}/test/test.c)
target_include_directories(test1 PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH})
target_link_libraries(test1 libxlsreader)
add_test(NAME test COMMAND test1 WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/libxls)

add_executable(test2 ${LIBXLS_PATH}/test/test2.c)
target_include_directories(test2 PUBLIC ${PROJECT_BINARY_DIR} ${LIBXLS_C_INCLUDE_PATH})
target_link_libraries(test2 libxlsreader)
add_test(NAME test2 COMMAND test2)