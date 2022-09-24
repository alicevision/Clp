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

project(Clp LANGUAGES C CXX VERSION 1.17.7)

set(CMAKE_CXX_STANDARD 14)

include(GNUInstallDirs)
set(INSTALL_INCLUDE_DIR ${CMAKE_INSTALL_INCLUDEDIR}/Coin)
set(INSTALL_CMAKE_DIR ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

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

set(CLP_SOURCES
    src/CbcOrClpParam.cpp
    src/ClpCholeskyBase.cpp
    src/ClpCholeskyDense.cpp
    src/ClpCholeskyPardiso.cpp
    src/ClpCholeskyTaucs.cpp
    src/Clp_C_Interface.cpp
    src/ClpConstraint.cpp
    src/ClpConstraintLinear.cpp
    src/ClpConstraintQuadratic.cpp
    src/ClpDualRowDantzig.cpp
    src/ClpDualRowPivot.cpp
    src/ClpDualRowSteepest.cpp
    src/ClpDummyMatrix.cpp
    src/ClpDynamicExampleMatrix.cpp
    src/ClpDynamicMatrix.cpp
    src/ClpEventHandler.cpp
    src/ClpFactorization.cpp
    src/ClpGubDynamicMatrix.cpp
    src/ClpGubMatrix.cpp
    src/ClpHelperFunctions.cpp
    src/ClpInterior.cpp
    src/ClpLinearObjective.cpp
    src/ClpLsqr.cpp
    src/ClpMatrixBase.cpp
    src/ClpMessage.cpp
    src/ClpModel.cpp
    src/ClpNetworkBasis.cpp
    src/ClpNetworkMatrix.cpp
    src/ClpNode.cpp
    src/ClpNonLinearCost.cpp
    src/ClpObjective.cpp
    src/ClpPackedMatrix.cpp
    src/ClpParam.cpp
    src/ClpParameters.cpp
    src/ClpParamUtils.cpp
    src/ClpPdcoBase.cpp
    src/ClpPdco.cpp
    src/ClpPEDualRowDantzig.cpp
    src/ClpPEDualRowSteepest.cpp
    src/ClpPEPrimalColumnDantzig.cpp
    src/ClpPEPrimalColumnSteepest.cpp
    src/ClpPESimplex.cpp
    src/ClpPlusMinusOneMatrix.cpp
    src/ClpPredictorCorrector.cpp
    src/ClpPresolve.cpp
    src/ClpPrimalColumnDantzig.cpp
    src/ClpPrimalColumnPivot.cpp
    src/ClpPrimalColumnSteepest.cpp
    src/ClpQuadraticObjective.cpp
    src/ClpSimplex.cpp
    src/ClpSimplexDual.cpp
    src/ClpSimplexNonlinear.cpp
    src/ClpSimplexOther.cpp
    src/ClpSimplexPrimal.cpp
    src/ClpSolve.cpp
    src/ClpSolver.cpp
    src/Idiot.cpp
    src/IdiSolve.cpp
    src/MyEventHandler.cpp
    src/MyMessageHandler.cpp
    src/OsiClp/OsiClpSolverInterface.cpp
    src/unitTest.cpp
)

# the following files are not included
#
# src/ClpCholeskyMumps.cpp
# src/ClpCholeskyUfl.cpp
# src/ClpCholeskyWssmp.cpp
# src/ClpCholeskyWssmpKKT.cpp
# src/ClpMain.cpp
# src/*Abc*.cpp

set(CLP_HEADERS
    src/CbcOrClpParam.hpp
    src/ClpCholeskyBase.hpp
    src/ClpCholeskyDense.hpp
    src/ClpCholeskyPardiso.hpp
    src/ClpCholeskyTaucs.hpp
    src/Clp_C_Interface.h
    src/ClpConfig.h
    src/ClpConstraint.hpp
    src/ClpConstraintLinear.hpp
    src/ClpConstraintQuadratic.hpp
    src/ClpDualRowDantzig.hpp
    src/ClpDualRowPivot.hpp
    src/ClpDualRowSteepest.hpp
    src/ClpDummyMatrix.hpp
    src/ClpDynamicExampleMatrix.hpp
    src/ClpDynamicMatrix.hpp
    src/ClpEventHandler.hpp
    src/ClpFactorization.hpp
    src/ClpGubDynamicMatrix.hpp
    src/ClpGubMatrix.hpp
    src/ClpHelperFunctions.hpp
    src/ClpInterior.hpp
    src/ClpLinearObjective.hpp
    src/ClpLsqr.hpp
    src/ClpMatrixBase.hpp
    src/ClpMessage.hpp
    src/ClpModel.hpp
    src/ClpModelParameters.hpp
    src/ClpNetworkBasis.hpp
    src/ClpNetworkMatrix.hpp
    src/ClpNode.hpp
    src/ClpNonLinearCost.hpp
    src/ClpObjective.hpp
    src/ClpPackedMatrix.hpp
    src/ClpParameters.hpp
    src/ClpParam.hpp
    src/ClpParamUtils.hpp
    src/ClpPdcoBase.hpp
    src/ClpPdco.hpp
    src/ClpPEDualRowDantzig.hpp
    src/ClpPEDualRowSteepest.hpp
    src/ClpPEPrimalColumnDantzig.hpp
    src/ClpPEPrimalColumnSteepest.hpp
    src/ClpPESimplex.hpp
    src/ClpPlusMinusOneMatrix.hpp
    src/ClpPredictorCorrector.hpp
    src/ClpPresolve.hpp
    src/ClpPrimalColumnDantzig.hpp
    src/ClpPrimalColumnPivot.hpp
    src/ClpPrimalColumnSteepest.hpp
    src/ClpPrimalQuadraticDantzig.hpp
    src/ClpQuadraticObjective.hpp
    src/ClpSimplexDual.hpp
    src/ClpSimplex.hpp
    src/ClpSimplexNonlinear.hpp
    src/ClpSimplexOther.hpp
    src/ClpSimplexPrimal.hpp
    src/ClpSolve.hpp
    src/ClpSolver.hpp
    src/config_clp_default.h
    src/Idiot.hpp
    src/MyEventHandler.hpp
    src/MyMessageHandler.hpp
    src/OsiClp/OsiClpConfig.h
    src/OsiClp/OsiClpSolverInterface.hpp
)

add_library(${PROJECT_NAME} ${CLP_SOURCES})

target_include_directories(${PROJECT_NAME}
                           PUBLIC
                           $<INSTALL_INTERFACE:${INSTALL_INCLUDE_DIR}>
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/>
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src/OsiClp>)

if(MSVC)
  target_compile_definitions(${PROJECT_NAME} PRIVATE _CRT_SECURE_NO_WARNINGS)
endif()
target_compile_definitions(${PROJECT_NAME} PRIVATE HAVE_CMATH)

target_link_libraries(${PROJECT_NAME} PRIVATE Coin::CoinUtils Coin::Osi)

install(FILES ${CLP_HEADERS} DESTINATION ${INSTALL_INCLUDE_DIR})

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
