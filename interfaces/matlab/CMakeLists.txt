project(qpOASES_Matlab)

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
include_directories(SYSTEM ${Matlab_INCLUDE_DIRS})

set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/mex)

if (CMAKE_VERSION GREATER 3.0.2)
    matlab_add_mex(
             NAME ${PROJECT_NAME}
             SRC qpOASES.cpp
             OUTPUT_NAME qpOASES
             LINK_TO qpOASES
         )
     
     # add_library(${PROJECT_NAME} ${LIBRARY_TYPE} qpOASES.cpp)
     # target_link_libraries(${PROJECT_NAME} ${Matlab_MEX_LIBRARY} ${Matlab_MX_LIBRARY}
     #                                       ${Matlab_ENG_LIBRARY} qpOASES)
          
     matlab_add_mex(
              NAME ${PROJECT_NAME}_sequence
              SRC qpOASES_sequence.cpp
              OUTPUT_NAME qpOASES_sequence
              LINK_TO qpOASES
          )                                  
               
else()

    add_library(${PROJECT_NAME} ${LIBRARY_TYPE} qpOASES.cpp)
    target_link_libraries(${PROJECT_NAME} ${Matlab_MEX_LIBRARY} ${Matlab_MX_LIBRARY}
                                          ${Matlab_ENG_LIBRARY} qpOASES)
                                      
    add_library(${PROJECT_NAME}_sequence ${LIBRARY_TYPE} qpOASES_sequence.cpp)
    target_link_libraries(${PROJECT_NAME}_sequence ${Matlab_MEX_LIBRARY} ${Matlab_MX_LIBRARY} 
                                        ${Matlab_ENG_LIBRARY} qpOASES)

    set_target_properties(${PROJECT_NAME} PROPERTIES SUFFIX .${Matlab_MEX_EXTENSION})
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")
    set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "qpOASES")

    set_target_properties(${PROJECT_NAME}_sequence PROPERTIES SUFFIX .${Matlab_MEX_EXTENSION})
    set_target_properties(${PROJECT_NAME}_sequence PROPERTIES PREFIX "")
    set_target_properties(${PROJECT_NAME}_sequence PROPERTIES OUTPUT_NAME "qpOASES_sequence")

endif()

set(MATLAB_SRCS ${CMAKE_CURRENT_SOURCE_DIR}/qpOASES.m
                ${CMAKE_CURRENT_SOURCE_DIR}/qpOASES_options.m 
                ${CMAKE_CURRENT_SOURCE_DIR}/qpOASES_sequence.m)

add_custom_target(${PROJECT_NAME}_copy_mfiles ALL)
foreach(Mfile ${MATLAB_SRCS})
    add_custom_command(TARGET ${PROJECT_NAME}_copy_mfiles POST_BUILD
                       COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_BINARY_DIR}/bin
                       COMMAND ${CMAKE_COMMAND} -E copy ${Mfile} ${CMAKE_BINARY_DIR}/bin
                       COMMENT "Copying ${Mfile} to ${CMAKE_BINARY_DIR}/bin")
endforeach()

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

install(TARGETS ${PROJECT_NAME}_sequence
  LIBRARY DESTINATION mex
  ARCHIVE DESTINATION mex
  RUNTIME DESTINATION mex
)
set_target_properties(
    ${PROJECT_NAME}_sequence
    PROPERTIES
    SOVERSION ${PACKAGE_SO_VERSION}
    )

    
install(FILES ${MATLAB_SRCS} DESTINATION bin)
