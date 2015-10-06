cmake_minimum_required(VERSION 2.8.3)
project(vrpn_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/vrpn)

ExternalProject_Add(vrpn_src
  URL https://github.com/vrpn/vrpn/archive/version_07.31.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake ../vrpn_src -DVRPN_BUILD_SERVERS:BOOL=OFF
                                      -DVRPN_BUILD_SERVER_LIBRARY:BOOL=OFF
                                      -DCMAKE_OSX_ARCHITECTURES:STRING=x86_64
                                      -DVRPN_BUILD_JAVA:BOOL=OFF
                                      -DVRPN_BUILD_PYTHON_HANDCODED_2X:BOOL=OFF
                                      -DVRPN_BUILD_PYTHON:BOOL=OFF
  BUILD_COMMAND make -j8
  INSTALL_COMMAND cp libvrpn.a ${CATKIN_DEVEL_PREFIX}/lib/ &&
                  cp ../vrpn_src/vrpn_Tracker.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_Connection.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_Configure.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_Shared.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_Types.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_BaseClass.h ${CATKIN_DEVEL_PREFIX}/include/vrpn
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} vrpn_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libvrpn.a)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/vrpn
  CFG_EXTRAS vrpn-extras.cmake)
