project(qpOASES_Simulink)

find_package(Matlab	 REQUIRED
                     MX_LIBRARY
                     ENG_LIBRARY)

if(Matlab_FOUND)
     message(STATUS "Matlab found")
else()
     message(FATAL_ERROR "Matlab not found")
endif()

include(AddInstallRPATHSupport)
add_install_rpath_support(BIN_DIRS ${CMAKE_INSTALL_PREFIX}/bin
                          LIB_DIRS ${CMAKE_INSTALL_PREFIX}/mex
                          DEPENDS QPOASES_ENABLE_RPATH
                          USE_LINK_PATH)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(SYSTEM ${Matlab_INCLUDE_DIRS} "${Matlab_ROOT_DIR}/simulink/include")

add_definitions(-DMATLAB_MEX_FILE)
set(SRCS qpOASES_QProblem.cpp qpOASES_QProblemB.cpp qpOASES_SQProblem.cpp)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/mex)

matlab_add_mex(
         NAME qpOASES_QProblem
         SRC qpOASES_QProblem.cpp
         LINK_TO qpOASES
     )
matlab_add_mex(
      NAME qpOASES_QProblemB
      SRC qpOASES_QProblemB.cpp
      LINK_TO qpOASES
  )
matlab_add_mex(
       NAME qpOASES_SQProblem
       SRC qpOASES_SQProblem.cpp
       LINK_TO qpOASES
   )
install(TARGETS qpOASES_QProblem qpOASES_QProblemB qpOASES_SQProblem 
  LIBRARY DESTINATION mex
  ARCHIVE DESTINATION mex
  RUNTIME DESTINATION mex
)

set_target_properties(
    qpOASES_QProblem
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )
set_target_properties(
    qpOASES_QProblemB
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )

set_target_properties(
    qpOASES_SQProblem
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )
