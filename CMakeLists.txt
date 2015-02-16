cmake_minimum_required(VERSION 2.8.3)
project(metis_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
ExternalProject_Add(metis_src
  URL https://github.com/ethz-asl/thirdparty_library_binaries/raw/master/metis-5.1.0.tar.gz
  UPDATE_COMMAND ""
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX} -DCMAKE_BUILD_TYPE:STRING=Release
  PATCH_COMMAND cd ../metis_src/ && patch -N CMakeLists.txt ${CMAKE_CURRENT_SOURCE_DIR}/CMakeLists.txt.patch  && patch -N libmetis/CMakeLists.txt ${CMAKE_CURRENT_SOURCE_DIR}/CMakeLists.txt_metis.patch
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} metis_src)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/libmetis${CMAKE_STATIC_LIBRARY_SUFFIX})

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS metis-extras.cmake)
