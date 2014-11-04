cmake_minimum_required(VERSION 2.8.3)
project(vrpn_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/vrpn)

ExternalProject_Add(vrpn_src
  URL http://www.cs.unc.edu/Research/vrpn/downloads/vrpn_07_31.zip
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cmake ../vrpn_src
  BUILD_COMMAND make -j8
  INSTALL_COMMAND cp libvrpn.a ${CATKIN_DEVEL_PREFIX}/lib/ &&
                  cp ../vrpn_src/vrpn_Tracker.h ${CATKIN_DEVEL_PREFIX}/include/vrpn &&
                  cp ../vrpn_src/vrpn_Connection.h ${CATKIN_DEVEL_PREFIX}/include/vrpn
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} vrpn_src)
target_link_libraries(${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/lib/libvrpn.a)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/vrpn
  CFG_EXTRAS vrpn-extras.cmake)