project(qpOASES_Simulink)

find_package(Matlab	 REQUIRED
                     MX_LIBRARY
                     ENG_LIBRARY)

if(Matlab_FOUND)
     message("Matlab libraries found: ${Matlab_LIBRARIES}")
     message("Matlab mex library: " ${Matlab_MEX_LIBRARY})
     message("Matlab mx  library: " ${Matlab_MX_LIBRARY})
     message("Matlab ENG library: " ${Matlab_ENG_LIBRARY})
else()
     message(FATAL_ERROR "Matlab not found")
endif()

include(AddInstallRPATHSupport)
add_install_rpath_support(BIN_DIRS ${CMAKE_INSTALL_PREFIX}/bin
                          LIB_DIRS ${CMAKE_INSTALL_PREFIX}/mex
                          DEPENDS QPOASES_ENABLE_RPATH
                          USE_LINK_PATH)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(SYSTEM ${Matlab_INCLUDE_DIRS} "${Matlab_ROOT}/simulink/include")

set(SRCS qpOASES_QProblem.cpp qpOASES_QProblemB.cpp qpOASES_SQProblem.cpp)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/mex)

matlab_add_mex(
         NAME ${PROJECT_NAME}
         SRC ${SRCS}
         OUTPUT_NAME qpOASES_simulink
         LINK_TO qpOASES
     )

install(TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION mex
  ARCHIVE DESTINATION mex
  RUNTIME DESTINATION mex
)
set_target_properties(
    ${PROJECT_NAME}
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )
    
