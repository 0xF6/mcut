#
# Copyright (c) 2021-2022 Floyd M. Chitalu.
# All rights reserved.
# 
# NOTE: This file is licensed under GPL-3.0-or-later (default). 
# A commercial license can be purchased from Floyd M. Chitalu. 
#  
# License details:
# 
# (A)  GNU General Public License ("GPL"); a copy of which you should have 
#      recieved with this file.
# 	    - see also: <http://www.gnu.org/licenses/>
# (B)  Commercial license.
#      - email: floyd.m.chitalu@gmail.com
# 
# The commercial license options is for users that wish to use MCUT in 
# their products for comercial purposes but do not wish to release their 
# software products under the GPL license. 
# 
# Author(s)     : Floyd M. Chitalu
#

cmake_minimum_required(VERSION 3.10)

project(mcut_tests VERSION 1.0)

find_package(Threads REQUIRED) # multi-context tests 

### Require out-of-source builds
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)

if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "[MCUT] You cannot build in a source directory.")
endif()

enable_testing()

# 
# Download and unpack utest at configure time
#
#download_project(PROJ                utest
#                 GIT_REPOSITORY      https://github.com/sheredom/utest.h.git
#                 GIT_TAG             master
#                 ${UPDATE_DISCONNECTED_IF_AVAILABLE}
#)
FetchContent_Populate(
    utest
    GIT_REPOSITORY 			https://github.com/sheredom/utest.h.git
    GIT_TAG       	master
    GIT_PROGRESS TRUE
)

set(utest_include_dir ${utest_SOURCE_DIR})

add_executable(
    mcut_tests 
    ${CMAKE_CURRENT_SOURCE_DIR}/source/main.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/booleanOperation.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/degenerateInput.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/benchmark.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/createContext.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/computeSeams.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/debugCallback.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/debugVerboseLog.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/dispatchFilterFlags.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/getContextInfo.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/getDataMaps.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/polygonWithHoles.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/triangulation.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/off.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/getEventInfo.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/concurrentSynchronizedContexts.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/userEvents.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/source/perturbationState.cpp)

target_include_directories(mcut_tests PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include ${MCUT_INCLUDE_DIR} ${utest_include_dir} ${libigl_include_dir} ${eigen_include_dir})
target_link_libraries(mcut_tests PRIVATE mcut Threads::Threads)
target_compile_definitions(mcut_tests PRIVATE -DMESHES_DIR="${CMAKE_CURRENT_SOURCE_DIR}/meshes" )
target_compile_options(mcut_tests PRIVATE ${compilation_flags})
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang" OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    target_compile_options(mcut_tests PRIVATE -Wno-class-memaccess) # utest: warning: ‘void* memset(void*, int, size_t)’ clearing an object of non-trivial type
endif()

if(MCUT_BUILD_AS_SHARED_LIB)
	target_compile_definitions(mcut_tests PRIVATE -DMCUT_WITH_ARBITRARY_PRECISION_NUMBERS=1 )
endif()