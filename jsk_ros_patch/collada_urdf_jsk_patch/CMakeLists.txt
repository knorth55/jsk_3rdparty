# http://ros.org/doc/groovy/api/catkin/html/user_guide/supposed.html
cmake_minimum_required(VERSION 2.8.3)
project(collada_urdf_jsk_patch)

if(("$ENV{ROS_DISTRO}" STREQUAL "groovy") OR ("$ENV{ROS_DISTRO}" STREQUAL "hydro"))
  set(SOURCE_DISTRO hydro-devel)
elseif ("$ENV{ROS_DISTRO}" STREQUAL "indigo" OR "$ENV{ROS_DISTRO}" STRGREATER "indigo")
  set(SOURCE_DISTRO bd4fc369d56eaa0c31d8cb17677e00b9d9685de6) # 1.11.13, before strip indigo
endif()
if ("$ENV{ROS_DISTRO}" STREQUAL "kinetic" OR "$ENV{ROS_DISTRO}" STRGREATER "kinetic")
    set(CXXFLAGS CXXFLAGS=-std=gnu++11)
endif()

find_package(catkin REQUIRED COMPONENTS collada_urdf)

catkin_package(CATKIN_DEPENDS collada_urdf)


set(ENV{PKG_CONFIG_PATH} ${CATKIN_DEVEL_PREFIX}/lib/pkgconfig:$ENV{PKG_CONFIG_PATH})
add_custom_target(urdf_to_collada ALL DEPENDS ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/urdf_to_collada)
add_custom_command(OUTPUT ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/urdf_to_collada
  DEPENDS Makefile
  COMMAND cmake -E chdir ${CMAKE_CURRENT_BINARY_DIR} make -f ${PROJECT_SOURCE_DIR}/Makefile PKG_CONFIG_PATH=${CATKIN_DEVEL_PREFIX}/lib/pkgconfig:$ENV{PKG_CONFIG_PATH} PATCH_DIR=${PROJECT_SOURCE_DIR} SOURCE_DISTRO=${SOURCE_DISTRO} ${CXXFLAGS}
  COMMAND cmake -E make_directory ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}
  COMMAND cmake -E copy ${CMAKE_CURRENT_BINARY_DIR}/urdf_to_collada ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}
  COMMAND cmake -E copy ${CMAKE_CURRENT_BINARY_DIR}/collada_to_urdf ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION})

add_dependencies(urdf_to_collada libassimp_devel)

install(PROGRAMS ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/urdf_to_collada ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/collada_to_urdf
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})





