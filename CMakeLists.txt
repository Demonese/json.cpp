cmake_minimum_required(VERSION 3.5)
project(json_cpp)

# options

set(JART_JSON_CPP_LIBRARY_NAME ${CMAKE_PROJECT_NAME} CACHE STRING "CMake target name and library name of json.cpp, default to 'json_cpp' (project name)")
option(JART_JSON_CPP_ENABLE_TEST "enable json.cpp test" ON)
option(JART_JSON_CPP_ENABLE_TEST_JSON_TEST_SUITE "enable json.cpp json test suite" ON)

# library: double conversion

set(DOUBLE_CONVERSION_LIBRARY_NAME "${JART_JSON_CPP_LIBRARY_NAME}_double_conversion")
add_library(${DOUBLE_CONVERSION_LIBRARY_NAME} STATIC)
target_compile_features(${DOUBLE_CONVERSION_LIBRARY_NAME} PRIVATE cxx_std_17)
target_include_directories(${DOUBLE_CONVERSION_LIBRARY_NAME} PUBLIC double-conversion)
target_sources(${DOUBLE_CONVERSION_LIBRARY_NAME} PRIVATE
    double-conversion/bignum-dtoa.cc
    double-conversion/bignum-dtoa.h
    double-conversion/bignum.cc
    double-conversion/bignum.h
    double-conversion/cached-powers.cc
    double-conversion/cached-powers.h
    double-conversion/diy-fp.h
    double-conversion/double-conversion.h
    double-conversion/double-to-string.cc
    double-conversion/double-to-string.h
    double-conversion/fast-dtoa.cc
    double-conversion/fast-dtoa.h
    double-conversion/fixed-dtoa.cc
    double-conversion/fixed-dtoa.h
    double-conversion/ieee.h
    double-conversion/string-to-double.cc
    double-conversion/string-to-double.h
    double-conversion/strtod.cc
    double-conversion/strtod.h
    double-conversion/utils.h
)

# library: json.cpp

set(LIBRARY_NAME ${JART_JSON_CPP_LIBRARY_NAME})
add_library(${LIBRARY_NAME} STATIC)
target_compile_features(${LIBRARY_NAME} PRIVATE cxx_std_17)
target_include_directories(${LIBRARY_NAME} PUBLIC .)
target_sources(${LIBRARY_NAME} PRIVATE
    jtckdint.h
    json.h
    json.cpp
)
target_link_libraries(${LIBRARY_NAME} INTERFACE ${DOUBLE_CONVERSION_LIBRARY_NAME})

# test: benchmark

if (JART_JSON_CPP_ENABLE_TEST)
    set(TEST_NAME "${JART_JSON_CPP_LIBRARY_NAME}_test")
    add_executable(${TEST_NAME})
    target_compile_features(${TEST_NAME} PRIVATE cxx_std_17)
    target_sources(${TEST_NAME} PRIVATE json_test.cpp)
    target_link_libraries(${TEST_NAME} PRIVATE ${LIBRARY_NAME})
endif ()

# test: json test suite

if (JART_JSON_CPP_ENABLE_TEST_JSON_TEST_SUITE)
    set(TEST_JSON_TEST_SUITE_NAME "${JART_JSON_CPP_LIBRARY_NAME}_test_json_test_suite")
    add_executable(${TEST_JSON_TEST_SUITE_NAME})
    target_compile_features(${TEST_JSON_TEST_SUITE_NAME} PRIVATE cxx_std_17)
    target_sources(${TEST_JSON_TEST_SUITE_NAME} PRIVATE jsontestsuite_test.cpp)
    target_link_libraries(${TEST_JSON_TEST_SUITE_NAME} PRIVATE ${LIBRARY_NAME})
endif ()
