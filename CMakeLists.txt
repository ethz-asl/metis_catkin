cmake_minimum_required(VERSION 2.8.3)
project(metis_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
ExternalProject_Add(metis_src
  URL https://github.com/ethz-asl/thirdparty_library_binaries/raw/master/metis-5.1.0.tar.gz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../metis_src && make config shared=1 prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../metis_src && (make || make VERBOSE=1 || true)  # Hack to fix metis make files.
  INSTALL_COMMAND ""
)


cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} metis_src)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/libmetis${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS metis-extras.cmake)
