# -----------------------------------------------
# config section
# -----------------------------------------------
configure_file (${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
        ${CMAKE_CURRENT_SOURCE_DIR}/config.h)

# -----------------------------------------------
# compiling section
# -----------------------------------------------
include_directories (. ${THIRDPARTY_DIR}/boost/include)

set (OPT_FILES "")

if (CMAKE_Fortran_COMPILER)
    set (OPT_FILES      lbfgs.f lbfgstrainer.cpp)
else (CMAKE_Fortran_COMPILER)
    message (STATUS
        "[Warning] Fortran compiler not available, "
        "LBFGS module will not be built.")
endif (CMAKE_Fortran_COMPILER)

set (maxent_lib_SRC
    display.cpp
    modelfile.cpp
    trainer.cpp
    gistrainer.cpp
    maxentmodel.cpp
    mmapfile.c
    lbfgs_wrapper.c
    ${OPT_FILES})

add_library (maxent_lib ${maxent_lib_SRC})
set_target_properties (maxent_lib PROPERTIES OUTPUT_NAME maxent)

set (GETOPT_FILES "")

if (WIN32 OR MINGW)
    set (GETOPT_FILES getopt.c getopt1.c)
endif ()

set (maxent_exe_SRC
    maxent.cpp
    display.cpp
    maxent_cmdline.c
    f77_dummy_main.cpp
    ${GETOPT_FILES})

add_executable (maxent_exe ${maxent_exe_SRC})

target_link_libraries (maxent_exe maxent_lib ${LINKER_LIBRARIES})

set_target_properties (maxent_exe 
    PROPERTIES 
    OUTPUT_NAME maxent
    RUNTIME_OUTPUT_DIRECTORY  ${TOOLS_DIR}/train/)
