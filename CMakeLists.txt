cmake_minimum_required(VERSION 3.25)

project(exprtk
        DESCRIPTION "exprtk with cmake" VERSION 1.0.0
)
set(BUILD_TESTS OFF CACHE BOOL "Build tests")
set(BUILD_BENCHMARK OFF CACHE BOOL "Build benchmark")
set(BUILD_EXAMPLES OFF CACHE BOOL "Build examples")

set(namespace exprtk)
set(CMAKE_CXX_STANDART 23)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

set(CMAKE_DEBUG_POSTFIX d)
add_library(exprtk exprtk.hpp)
if(${BUILD_TESTS})
        add_executable(tests)
        target_sources(tests PRIVATE tests/exprtk_test.cpp PRIVATE FILE_SET HEADERS BASE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}")
endif()

if(${BUILD_BENCHMARK})
        add_executable(benchmark)
        target_sources(benchmark PRIVATE benchmark/exprtk_benchmark.cpp PRIVATE FILE_SET HEADERS BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR})
endif()

if(${BUILD_TESTS})
        set(EXAMPLES 
        "exprtk_simple_example_01;"  "exprtk_simple_example_09;"  "exprtk_simple_example_17;"
        "exprtk_simple_example_02;"  "exprtk_simple_example_10;"  "exprtk_simple_example_18;"
        "exprtk_simple_example_03;"  "exprtk_simple_example_11;"  "exprtk_simple_example_19;"
        "exprtk_simple_example_04;"  "exprtk_simple_example_12;"  "exprtk_simple_example_20;"
        "exprtk_simple_example_05;"  "exprtk_simple_example_13;"  "exprtk_simple_example_21;"
        "exprtk_simple_example_06;"  "exprtk_simple_example_14;"  "exprtk_simple_example_22;"
        "exprtk_simple_example_07;"  "exprtk_simple_example_15;"  "exprtk_simple_example_23;"
        "exprtk_simple_example_08;"  "exprtk_simple_example_16;"  "exprtk_simple_example_24")

        foreach(example IN ITEMS ${EXAMPLES})
                add_executable("${example}")
                target_sources("${example}" PRIVATE "example/${example}.cpp" PRIVATE FILE_SET HEADERS BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR})
                target_compile_options("${example}" PRIVATE -Wall -Wextra)
                target_link_libraries("${example}" exprtk)
        endforeach()
endif()


add_library(${namespace}::exprtk ALIAS exprtk)

set_target_properties(exprtk
        PROPERTIES VERSION ${PROJECT_VERSION} 
        SOVERSION ${PROJECT_VERSION_MAJOR})

set_target_properties(exprtk PROPERTIES LINKER_LANGUAGE CXX)

include(GenerateExportHeader)

generate_export_header(
   exprtk  
    EXPORT_FILE_NAME
    export/exprtk/exprtk.hpp
)

install(
        TARGETS exprtk
        EXPORT exprtkTargets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(
        FILES "${CMAKE_CURRENT_BINARY_DIR}/export/exprtk/exprtk.hpp"
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/exprtk
)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
        "exprtkConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)

configure_package_config_file(
    "${CMAKE_CURRENT_LIST_DIR}/cmake/exprtkConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/exprtk/Config.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/cmake/exprtk
)

# install the CMake targets
install(
        EXPORT exprtkTargets
        FILE exprtkTargets.cmake
        NAMESPACE exprtk::
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/cmake/exprtk
)

# Define variables for the pkg-config file
set(PACKAGE_NAME exprtk)
if(ENABLE_WIDE_CHAR)
  set(PKG_CONFIG_FLAGS "-D_UNICODE")
endif(ENABLE_WIDE_CHAR)
configure_file(
    exprtk.pc.in
    ${CMAKE_BINARY_DIR}/exprtk.pc
    @ONLY
)
install(
    FILES ${CMAKE_BINARY_DIR}/exprtk.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)
