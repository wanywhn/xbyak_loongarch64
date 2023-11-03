#*******************************************************************************
# Copyright 2021 LOONGSON LIMITED
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#*******************************************************************************

    cmake_minimum_required (VERSION 3.8)

    # file(READ xbyak_loongarch64/xbyak_loongarch64_version.h VERSION_FILE)
    # string(REGEX MATCH "[0-9]+\.[0-9]+\.[0-9]+" VERSION ${VERSION_FILE})
    # string(REGEX MATCH [0-9]+\.[0-9]+ VERSION_SUB ${VERSION})
    #message("VERSION ${VERSION}")
    #message("VERSION_SUB ${VERSION_SUB}")
    set(VERSION 1)
    set(VERSION_SUB 0)

    
    project(xbyak_loongarch64
        VERSION ${VERSION}
        LANGUAGES CXX)

    option(
        SHARED
        "Build shared library"
        OFF
    )

    if(NOT DEFINED CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE "Release")
    endif()
    
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
    
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
    
    add_library(xbyak_loongarch64 STATIC src/xbyak_loongarch64_impl.cpp )#src/util_impl.cpp)
    add_library(xbyak_loongarch64::xbyak_loongarch64 ALIAS xbyak_loongarch64)
    target_include_directories(xbyak_loongarch64 PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/xbyak_loongarch64/>
        $<INSTALL_INTERFACE:$CMAKE_INSTALL_DIR/include>)
    set_target_properties(xbyak_loongarch64 PROPERTIES
        POSITION_INDEPENDENT_CODE ON)
    
    if(SHARED)
    add_library(xbyak_loongarch64_sh SHARED src/xbyak_loongarch64_impl.cpp)
    add_library(xbyak_loongarch64::xbyak_loongarch64_sh ALIAS xbyak_loongarch64_sh)
    target_include_directories(xbyak_loongarch64_sh PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/xbyak_loongarch64/>
        $<INSTALL_INTERFACE:$CMAKE_INSTALL_DIR/include>)
    set_target_properties(xbyak_loongarch64_sh PROPERTIES
        POSITION_INDEPENDENT_CODE ON)
    set_target_properties(xbyak_loongarch64_sh PROPERTIES
        OUTPUT_NAME xbyak_loongarch64
        VERSION ${VERSION}
        SOVERSION ${VERSION_SUB})
    endif()

    install(TARGETS xbyak_loongarch64
        EXPORT xbyak_loongarch64Targets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
    )
    if(SHARED)
    install(TARGETS xbyak_loongarch64_sh
        EXPORT xbyak_loongarch64Targets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
    )
    endif()
    
    install(DIRECTORY ${CMAKE_SOURCE_DIR}/xbyak_loongarch64
        DESTINATION include
        FILES_MATCHING PATTERN "*.h"
    )
    
    install(EXPORT xbyak_loongarch64Targets
        FILE xbyak_loongarch64Targets.cmake
        NAMESPACE xbyak_loongarch64::
        DESTINATION lib/cmake/xbyak_loongarch64)
    
    # support local build-tree export to allow import from external projects
    export(EXPORT xbyak_loongarch64Targets
        FILE xbyak_loongarch64Targets.cmake
        NAMESPACE xbyak_loongarch64::)
    set(CMAKE_EXPORT_PACKAGE_REGISTRY ON)
    export(PACKAGE xbyak_loongarch64 )
