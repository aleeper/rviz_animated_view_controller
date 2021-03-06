cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
rosbuild_make_distribution(0.1.0)

include($ENV{ROS_ROOT}/core/rosbuild/FindPkgConfig.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

rosbuild_init()

rosbuild_add_boost_directories()

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

find_package(Qt4 COMPONENTS QtCore QtGui QtOpenGL REQUIRED)
include(${QT_USE_FILE})

add_definitions(-DQT_NO_KEYWORDS)
# aleeper: I added these to disable tons of warnings associated with OgreException.h
#          when compiling on 12.04. We should figure out a safer way of removing them though...
add_definitions(-Wno-unused-but-set-parameter -Wno-unknown-pragmas)


find_package(OpenGL REQUIRED)
include_directories( ${OPENGL_INCLUDE_DIR} )

find_package(PythonLibs REQUIRED)
include_directories(${PYTHON_INCLUDE_PATH})

pkg_check_modules(OGRE OGRE)
include_directories( ${OGRE_INCLUDE_DIRS} )
link_directories( ${OGRE_LIBRARY_DIRS} )

qt4_wrap_cpp(MOC_FILES
  include/${PROJECT_NAME}/rviz_animated_view_controller.h
)

set(SOURCE_FILES
    src/rviz_animated_view_controller.cpp
    ${MOC_FILES}
)


rosbuild_add_library(${PROJECT_NAME}
                      ${SOURCE_FILES}
                      manifest.xml
                      plugin_description.xml
                      )

target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${OGRE_LIBRARIES})
rosbuild_link_boost(${PROJECT_NAME} thread signals filesystem system program_options)

