cmake_minimum_required (VERSION 2.8.0)
project ("LTP - Language Technology Platform")

# project attributes section
# -- config cmake modules path
set (CMAKE_MODULE_PATH      ${PROJECT_SOURCE_DIR}/cmake)

# -- config output directories
set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set (LIBRARY_OUTPUT_PATH    ${PROJECT_SOURCE_DIR}/lib)
set (INCLUDE_OUTPUT_PATH    ${PROJECT_SOURCE_DIR}/include)

# -- config source directories
set (SOURCE_DIR             ${PROJECT_SOURCE_DIR}/src)
set (THIRDPARTY_DIR         ${PROJECT_SOURCE_DIR}/thirdparty)
set (TOOLS_DIR              ${PROJECT_SOURCE_DIR}/tools)

# -- config resource directories
set (CONFIGURE_DIR          ${PROJECT_SOURCE_DIR}/conf)
set (MODEL_DIR              ${PROJECT_SOURCE_DIR}/ltp_data)
set (DATA_DIR               ${PROJECT_SOURCE_DIR}/test_data)

# -- fortran checking --
set ( LINKER_LIBRARIES  "" )

include (CMakeDetermineFortranCompiler)
if (CMAKE_Fortran_COMPILER)
    set (HAVE_FORTRAN   1)
    enable_language (Fortran)

    set (LINKER_LIBRARIES ${LINKER_LIBRARIES} gfortran)
endif (CMAKE_Fortran_COMPILER)

# -- library header and system call checking
include (ConfigureChecks)

check_function_exists ("getopt_long"    HAVE_GETOPT_LONG)
check_function_exists ("getpagesize"    HAVE_GETPAGESIZE)
check_function_exists ("gettimeofday"   HAVE_GETTIMEOFDAY)
check_function_exists ("getpagesize"    HAVE_GETPAGESIZE)
check_function_exists ("mmap"           HAVE_MMAP)

check_include_file_concat ("ieeefp.h"       HAVE_IEEEFP_H)
check_include_file_concat ("ctype.h"        HAVE_CTYPE_H)
check_include_file_concat ("dlfcn.h"        HAVE_DLFCN_H)
check_include_file_concat ("fcntl.h"        HAVE_FCNTL_H)
check_include_file_concat ("inttypes.h"     HAVE_INTTYPES_H)
check_include_file_concat ("math.h"         HAVE_MATH_H)
check_include_file_concat ("memory.h"       HAVE_MEMORY_H)
check_include_file_concat ("pthread.h"      HAVE_PTHREAD_H)
check_include_file_concat ("stdint.h"       HAVE_STDINT_H)
check_include_file_concat ("stdlib.h"       HAVE_STDLIB_H)
check_include_file_concat ("strings.h"      HAVE_STRINGS_H)
check_include_file_concat ("string.h"       HAVE_STRING_H)
check_include_file_concat ("sys/mman.h"     HAVE_SYS_MMAN_H)
check_include_file_concat ("sys/stat.h"     HAVE_SYS_STAT_H)
check_include_file_concat ("sys/times.h"    HAVE_SYS_TIMES_H)
check_include_file_concat ("sys/types.h"    HAVE_SYS_TYPES_H)
check_include_file_concat ("unistd.h"       HAVE_UNISTD_H)

check_library_exists_concat ("z"            gzopen          HAVE_LIBZ)
check_library_exists_concat ("m"            random          HAVE_LIBM)
check_library_exists_concat ("gnugetopt"    getopt          HAVE_LIBGNUGETOPT)
check_library_exists_concat ("pthread"      pthread_create  HAVE_LIBPTHREAD)

set ( CMAKE_C_FLAGS     "${CMAKE_C_FLAGS}   -DHAVE_CONFIG_H")
set ( CMAKE_CXX_FLAGS   "${CMAKE_CXX_FLAGS} -DHAVE_CONFIG_H")

if (HAVE_LIBPTHREAD)
    set (LINKER_LIBRARIES ${LINKER_LIBRARIES} pthread)
endif (HAVE_LIBPTHREAD)

if (HAVE_LIBZ)
    set (LINKER_LIBRARIES ${LINKER_LIBRARIES} z)
endif (HAVE_LIBZ)

if (HAVE_LIBM)
    set (LINKER_LIBRARIES ${LINKER_LIBRARIES} m)
endif (HAVE_LIBM)

# compiling section
# -- compile shipped libraries
add_subdirectory (thirdparty)

# -- compile source code
add_subdirectory (src)

# testing section
# -- generate configure file for ltp_test
configure_file (
    ${CONFIGURE_DIR}/ltp_all_modules.conf.in
    ${EXECUTABLE_OUTPUT_PATH}/ltp_all_modules.conf)

configure_file (
    ${TOOLS_DIR}/testing/auto-test.sh.in
    ${TOOLS_DIR}/testing/auto-test.sh)

configure_file (
    ${TOOLS_DIR}/train/ltp-model.in
    ${TOOLS_DIR}/train/ltp-model)

# enable test
#enable_testing()

# compile testing
add_subdirectory (test)
