##
##     This file is part of qpOASES.
##
##     qpOASES -- An Implementation of the Online Active Set Strategy.
##     Copyright (C) 2007-2015 by Hans Joachim Ferreau, Andreas Potschka,
##     Christian Kirches et al. All rights reserved.
##
##     qpOASES is free software; you can redistribute it and/or
##     modify it under the terms of the GNU Lesser General Public
##     License as published by the Free Software Foundation; either
##     version 2.1 of the License, or (at your option) any later version.
##
##     qpOASES is distributed in the hope that it will be useful,
##     but WITHOUT ANY WARRANTY; without even the implied warranty of
##     MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
##     See the GNU Lesser General Public License for more details.
##
##     You should have received a copy of the GNU Lesser General Public
##     License along with qpOASES; if not, write to the Free Software
##     Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA
##



##
##     Filename:  CMakeLists.txt
##     Author:    Hans Joachim Ferreau (thanks to Milan Vukov)
##     Version:   3.1
##     Date:      2007-2015
##

cmake_minimum_required(VERSION 2.8.11)

project(qpOASES CXX)
set(PACKAGE_NAME "qpOASES")
set(PACKAGE_VERSION "3.1.0")
set(PACKAGE_SO_VERSION "3.1")
set(PACKAGE_DESCRIPTION "QP solver using the online active set strategy")
set(PACKAGE_AUTHOR "Hans Joachim Ferreau, Andreas Potschka, Christian Kirches et al.")
set(PACKAGE_MAINTAINER "Hans Joachim Ferreau, Andreas Potschka, Christian Kirches et al.")
set(PACKAGE_URL "https://projects.coin-or.org/qpOASES")

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

set(BUILD_SHARED_LIBS ON)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

if( NOT CMAKE_VERBOSE_MAKEFILE )
    set( CMAKE_VERBOSE_MAKEFILE OFF )
endif( NOT CMAKE_VERBOSE_MAKEFILE )

if( NOT CMAKE_BUILD_TYPE )
    set(CMAKE_BUILD_TYPE Release CACHE STRING
        "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
        FORCE
        )
endif( NOT CMAKE_BUILD_TYPE )


############################################################
#################### compiler flags ########################
############################################################
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D__NO_COPYRIGHT__")
IF ( UNIX )
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pedantic -Wfloat-equal -Wshadow -DLINUX")
    SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_DEBUG} -O3 -finline-functions")
ELSEIF( WINDOWS )
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -nologo -EHsc -DWIN32")
ENDIF()

SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -D__DEBUG__")

############################################################
######################## rpath #############################
############################################################
option(QPOASES_ENABLE_RPATH "Enable RPATH for the library" TRUE)
mark_as_advanced(QPOASES_ENABLE_RPATH)

include(AddInstallRPATHSupport)
add_install_rpath_support(BIN_DIRS "${CMAKE_INSTALL_PREFIX}/bin"
                          LIB_DIRS "${CMAKE_INSTALL_PREFIX}/lib"
                          DEPENDS QPOASES_ENABLE_RPATH
                          USE_LINK_PATH)

############################################################
#################### build and install #####################
############################################################
include_directories(${PROJECT_SOURCE_DIR}/include)
file(GLOB HPPS include/qpOASES/*.hpp)
file(GLOB IPPS include/qpOASES/*.ipp)

set(HEADERS ${HPPS} ${IPPS} include/qpOASES.hpp)
source_group("Header Files" FILES include/qpOASES.hpp)
source_group("Header Files\\hpp" FILES ${HPPS})
source_group("Header Files\\ipp" FILES ${IPPS})

# compile qpOASES libraries
file(GLOB SRC src/*.cpp)

if(WIN32)
    set(LIBRARY_TYPE STATIC)
else()
    if (BUILD_SHARED_LIBS)
        set(LIBRARY_TYPE SHARED)
    else()
        set(LIBRARY_TYPE STATIC)
    endif()
endif()

# library
add_library(qpOASES ${LIBRARY_TYPE} ${SRC} ${HEADERS})
install(TARGETS qpOASES
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION lib
)
set_target_properties(
    qpOASES
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )

# headers
install(FILES include/qpOASES.hpp
  DESTINATION include)
install(DIRECTORY include/qpOASES
  DESTINATION include
  FILES_MATCHING PATTERN "*.hpp"
  PATTERN "*.ipp"
  PATTERN ".svn" EXCLUDE)

############################################################
######################### examples #########################
############################################################
#TODO: Move the examples in sub-folders to avoid cluttering the project
option(QPOASES_BUILD_EXAMPLES "Build examples" OFF)

if (QPOASES_BUILD_EXAMPLES)
    # compile qpOASES examples
    set(EXAMPLE_NAMES
        example1
        example1a
        example1b
        example2
        example3
        example3b
        example4
        example5
        exampleLP
        qrecipe
    )

    foreach(ELEMENT ${EXAMPLE_NAMES})
        ADD_EXECUTABLE(${ELEMENT} examples/${ELEMENT}.cpp)
        TARGET_LINK_LIBRARIES(${ELEMENT} qpOASES)
    endforeach(ELEMENT ${EXAMPLE_NAMES})
endif()

##
##   end of file
##

add_subdirectory(interfaces)
