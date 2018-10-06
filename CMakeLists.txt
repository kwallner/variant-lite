# Copyright 2016-2018 by Martin Moene
#
# https://github.com/martinmoene/variant-lite
#
# Distributed under the Boost Software License, Version 1.0. 
# (See accompanying file LICENSE.txt or copy at http://www.boost.org/LICENSE_1_0.txt)

cmake_minimum_required( VERSION 3.5 )

# variant-lite version, updated by script/update-version.py:

set( variant_lite_version "1.0.0" )

# At default, enable building and performing of tests and building of examples:

option( VARIANT_LITE_OPT_BUILD_TESTS    "Build and perform variant-lite tests" ON )
option( VARIANT_LITE_OPT_BUILD_EXAMPLES "Build variant-lite examples" OFF )

option( VARIANT_LITE_OPT_SELECT_STD     "Select std::variant"    OFF )
option( VARIANT_LITE_OPT_SELECT_NONSTD  "Select nonstd::variant" OFF )

project( variant_lite LANGUAGES CXX VERSION ${variant_lite_version})

include( GNUInstallDirs )

set( package_name "variant_lite" )

# Interface library:

add_library(                
    ${package_name} INTERFACE )
    
target_include_directories( 
    ${package_name} INTERFACE "$<BUILD_INTERFACE:${include_source_dir}>" "$<INSTALL_INTERFACE:include>")

# Installation:

install( 
    DIRECTORY ${CMAKE_SOURCE_DIR}/include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR} )

# If requested, build and perform tests, build examples:

enable_testing()

if ( VARIANT_LITE_OPT_BUILD_TESTS )
    add_subdirectory( test )
endif()

if ( VARIANT_LITE_OPT_BUILD_EXAMPLES )
    add_subdirectory( example )
endif()

#
# Create Config Files
#
install(TARGETS ${package_name}
    EXPORT Targets
    INCLUDES DESTINATION include
    )

install(EXPORT Targets
    FILE ${PROJECT_NAME}Targets.cmake
    DESTINATION share/cmake/${PROJECT_NAME}
    )

#
# Export Library
#
include(CMakePackageConfigHelpers)

write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMinorVersion
    )

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/script/LibraryConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    @ONLY
    )

install(
    FILES
        "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    DESTINATION
        . # Root Directory
    )

# end of file
