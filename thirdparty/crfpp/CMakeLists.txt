include (ConfigureChecks)

# -----------------------------------------------
# configure section
# -----------------------------------------------
set (PACKAGE            "CRF++[LTP-Shipped]")
set (PACKAGE_BUGREPORT  "yjliu@ir.hit.edu.cn")
set (VERSION            "0.50")

configure_file (${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
    ${CMAKE_CURRENT_SOURCE_DIR}/config.h)

set (crfpp_SRC
    common.h
    config.h
    config-win32.h
    crfpp.h
    darts.h
    encoder.h
    feature_cache.h
    feature_index.h
    freelist.h
    lbfgs.h
    mmap.h
    node.h
    param.h
    path.h
    scoped_ptr.h
    stream_wrapper.h
    tagger.h
    thread.h
    timer.h
    encoder.cpp
    feature_cache.cpp
    feature.cpp
    feature_index.cpp
    lbfgs.cpp
    libcrfpp.cpp
    node.cpp
    param.cpp
    path.cpp
    tagger.cpp)

add_library (crfpp ${crfpp_SRC})

# -----------------------------------------------
# add executables
# -----------------------------------------------

set (crf_learn_SRC  crf_learn.cpp)
add_executable (crf_learn   ${crf_learn_SRC})
target_link_libraries (crf_learn crfpp pthread)
set_target_properties (crf_learn
    PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY  ${TOOLS_DIR}/train)

set (crf_test_SRC   crf_test.cpp)
add_executable (crf_test    ${crf_test_SRC})
target_link_libraries (crf_test crfpp pthread)
set_target_properties (crf_test
    PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY  ${TOOLS_DIR}/train)


