cmake_minimum_required(VERSION 2.8.3)
project(realsense_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(realsense_src
  GIT_REPOSITORY https://github.com/IntelRealSense/librealsense.git
  GIT_TAG v2.36.0
  GIT_PROGRESS 1
  UPDATE_COMMAND ""
  #PATCH_COMMAND ""
  CONFIGURE_COMMAND cd ../realsense_src && cmake -DCMAKE_BUILD_TYPE=Release
                                             -DOpenCV_DIR=${CATKIN_DEVEL_PREFIX}/include/opencv2
                                             -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
                                             -DBUILD_WITH_CUDA=true
                                             -DFORCE_RSUSB_BACKEND=true .
  BUILD_COMMAND cd ../realsense_src && make -j6
  INSTALL_COMMAND cd ../realsense_src && make install
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/librealsense2
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "librealsense2*")

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES librealsense2)
