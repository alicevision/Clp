# Copyright (c) Microsoft Corporation
#
# All rights reserved.
#
# MIT License
#
# Permission is hereby granted, free of charge, to any person obtaining a copy of
# this software and associated documentation files (the "Software"), to deal in
# the Software without restriction, including without limitation the rights to
# use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
# of the Software, and to permit persons to whom the Software is furnished to do
# so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED *AS IS*, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

cmake_minimum_required(VERSION 3.11)

project(Clp LANGUAGES C CXX)

set(PROJECT_VERSION 1.16.11)
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 16)
set(PROJECT_VERSION_PATCH 11)

set(CMAKE_CXX_STANDARD 14)

set(INSTALL_BIN_DIR      "bin"                      CACHE PATH "Path where exe and dll will be installed")
set(INSTALL_LIB_DIR      "lib"                      CACHE PATH "Path where lib will be installed")
set(INSTALL_INCLUDE_DIR  "include/Coin"             CACHE PATH "Path where headers will be installed")
set(INSTALL_CMAKE_DIR    "share/clp"                CACHE PATH "Path where cmake configs will be installed")

# Make relative paths absolute (needed later on)
set(RELATIVE_INSTALL_INCLUDE_DIR ${INSTALL_INCLUDE_DIR})
foreach(p LIB BIN INCLUDE CMAKE)
  set(var INSTALL_${p}_DIR)
  if(NOT IS_ABSOLUTE "${${var}}")
    set(${var} "${CMAKE_INSTALL_PREFIX}/${${var}}")
  endif()
endforeach()

if(MSVC)
  set(
    CMAKE_CXX_FLAGS
    "${CMAKE_CXX_FLAGS} /bigobj /MP /wd4996 /wd4819 /wd4251 /wd4267 /wd4244 /wd4275"
    )
endif()

if(APPLE)
  set(
    CMAKE_CXX_FLAGS
    "${CMAKE_CXX_FLAGS} -Wno-inconsistent-missing-override -Wno-unused-command-line-argument -Wno-unused-result -Wno-exceptions"
    )
  set(CMAKE_OSX_DEPLOYMENT_TARGET "10.9"
      CACHE STRING "Minimum OS X deployment version")
endif()

find_package(CoinUtils REQUIRED)
find_package(Osi REQUIRED)

file(GLOB CLP_SOURCES
          src/*.hpp
          src/*.cpp
          src/OsiClp/*.hpp
          src/OsiClp/*.cpp)
file(GLOB CLP_REMOVE
          src/ClpCholeskyMumps.cpp
          src/ClpCholeskyUfl.cpp
          src/ClpCholeskyWssmp.cpp
          src/ClpCholeskyWssmpKKT.cpp
          src/ClpMain.cpp
          src/*Abc*.cpp
          src/ClpCholeskyMumps.hpp
          src/ClpCholeskyUfl.hpp
          src/ClpCholeskyWssmp.hpp
          src/ClpCholeskyWssmpKKT.hpp
          src/ClpMain.hpp
          src/*Abc*.hpp
          src/*Abc*.h)
list(REMOVE_ITEM CLP_SOURCES ${CLP_REMOVE})

add_library(${PROJECT_NAME} ${CLP_SOURCES})

target_include_directories(${PROJECT_NAME}
                           PUBLIC
                           $<INSTALL_INTERFACE:${RELATIVE_INSTALL_INCLUDE_DIR}>
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/>
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/OsiClp>)

if(MSVC)
  target_compile_definitions(${PROJECT_NAME} PRIVATE _CRT_SECURE_NO_WARNINGS)
endif()
target_compile_definitions(${PROJECT_NAME} PRIVATE HAVE_CMATH)
target_compile_definitions(${PROJECT_NAME} PUBLIC COIN_HAS_CLP)

target_link_libraries(${PROJECT_NAME} PRIVATE Coin::CoinUtils Coin::Osi)

install(DIRECTORY src/
        DESTINATION ${INSTALL_INCLUDE_DIR}
        FILES_MATCHING
        PATTERN "*.h"
        PATTERN "*.hpp"
        PATTERN "config_default.h" EXCLUDE)

install(DIRECTORY src/OsiClp/
        DESTINATION ${INSTALL_INCLUDE_DIR}
        FILES_MATCHING
        PATTERN "*.h"
        PATTERN "*.hpp"
        PATTERN "config_default.h" EXCLUDE)

install(TARGETS ${PROJECT_NAME}
        EXPORT "${PROJECT_NAME}Targets"
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin)

include(CMakePackageConfigHelpers)

set(version_config "${CMAKE_CURRENT_BINARY_DIR}/temp/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${CMAKE_CURRENT_BINARY_DIR}/temp/${PROJECT_NAME}Config.cmake")
set(namespace "Coin::")

write_basic_package_version_file("${version_config}"
                                 COMPATIBILITY SameMajorVersion)

configure_package_config_file("Config.cmake.in"
                              "${project_config}"
                              INSTALL_DESTINATION
                              "${INSTALL_CMAKE_DIR}")

install(FILES "${project_config}" "${version_config}"
        DESTINATION "${INSTALL_CMAKE_DIR}")

install(EXPORT "${PROJECT_NAME}Targets"
        NAMESPACE "${namespace}"
        DESTINATION "${INSTALL_CMAKE_DIR}")
