cmake_minimum_required(VERSION 2.8.10)
cmake_policy(SET CMP0015 NEW)


### project definition

set(PROJECT prt4maya_codec)
project(${PROJECT})
include(../../common.cmake)


### dependencies

find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIR})

find_package(prt CONFIG REQUIRED)
include_directories(${PRT_INCLUDE_PATH})


### build target

set(CMAKE_INCLUDE_CURRENT_DIR ON)

list(APPEND SOURCES codecs_maya.cpp encoder/MayaEncoder.cpp)
add_library(${PROJECT} SHARED ${SOURCES})
add_definitions(-DPRT_VERSION_MAJOR=${PRT_VERSION_MAJOR} -DPRT_VERSION_MINOR=${PRT_VERSION_MINOR})

if(PRT_WINDOWS)
	target_link_libraries(${PROJECT} IPHlpApi Psapi DbgHelp)
elseif(PRT_LINUX)
	target_link_libraries(${PROJECT} z pthread dl rt)
endif()

target_link_libraries(${PROJECT} ${PRT_LINK_LIBRARIES})
set_target_properties(${PROJECT} PROPERTIES LINKER_LANGUAGE CXX)


### install target

set(CMAKE_INSTALL_PREFIX "${PROJECT_SOURCE_DIR}/../install") 
install(TARGETS ${PROJECT} EXPORT "${PROJECT}Targets" RUNTIME DESTINATION lib LIBRARY DESTINATION lib)
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/encoder/IMayaCallbacks.h DESTINATION include)


### install config file and export target

install(EXPORT "${PROJECT}Targets" DESTINATION "cmake")
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/exportConfig.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT}Config.cmake COPYONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT}Config.cmake DESTINATION cmake)


