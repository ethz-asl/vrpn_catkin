cmake_minimum_required(VERSION 3.5)
project(vrpn_vendor)

find_package(ament_cmake REQUIRED)

include(ExternalProject)

ExternalProject_Add(vrpn_src
  GIT_REPOSITORY https://github.com/vrpn/vrpn
  GIT_TAG df744e3cb61ef390b0b2fd5382b5be0340f80914
  GIT_CONFIG advice.detachedHead=false
  UPDATE_COMMAND ""
  CMAKE_ARGS  -DVRPN_BUILD_SERVERS:BOOL=OFF
              -DCMAKE_OSX_ARCHITECTURES:STRING=x86_64
              -DVRPN_BUILD_JAVA:BOOL=OFF
              -DVRPN_BUILD_PYTHON_HANDCODED_2X:BOOL=OFF
              -DVRPN_BUILD_PYTHON:BOOL=OFF
              -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install
)

add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} vrpn_src)
target_link_libraries(${PROJECT_NAME} /lib/libvrpn.a ${CATKIN_DEVEL_PREFIX}/lib/libquat.a)

ament_export_include_directories(include)
ament_export_libraries(vrpn)
ament_export_dependencies(vrpn)

install(
  DIRECTORY
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_install/
  DESTINATION
    ${CMAKE_INSTALL_PREFIX}
  USE_SOURCE_PERMISSIONS
)

ament_package()