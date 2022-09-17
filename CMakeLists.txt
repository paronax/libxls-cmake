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
set(LIBXLS_C_SRC_PATH "libxls/Src")
set(LIBXLS_C_INCLUDE_PATH "${LIBXLS_PATH}/include")
set(LIBXLS_CXX_PATH "${LIBXLS_PATH}/cplusplus")

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