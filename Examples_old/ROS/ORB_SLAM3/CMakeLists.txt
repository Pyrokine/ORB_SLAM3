cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

rosbuild_init()

IF (NOT ROS_BUILD_TYPE)
    SET(ROS_BUILD_TYPE Release)
ENDIF ()

MESSAGE("Build type: " ${ROS_BUILD_TYPE})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3 -march=native")

# Check C++17 or C++0x support
include(CheckCXXCompilerFlag)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/../../../cmake_modules)

# SET(OpenCV_DIR /usr/local/opencv460)
find_package(OpenCV 4.2 REQUIRED)
MESSAGE("OpenCV VERSION: ${OpenCV_VERSION}, DIR: ${OpenCV_DIR}")

find_package(Eigen3 3.1.0 REQUIRED)
MESSAGE("Eigen3 VERSION: ${Eigen3_VERSION}, DIR: ${Eigen3_DIR}")

find_package(Pangolin REQUIRED)
MESSAGE("Pangolin VERSION: ${Pangolin_VERSION}, DIR: ${Pangolin_DIR}")

include_directories(
        ${PROJECT_SOURCE_DIR}
        ${PROJECT_SOURCE_DIR}/../../../
        ${PROJECT_SOURCE_DIR}/../../../include
        ${PROJECT_SOURCE_DIR}/../../../include/CameraModels
        ${PROJECT_SOURCE_DIR}/../../../Thirdparty/Sophus
        ${Pangolin_INCLUDE_DIRS}
)

set(LIBS
        ${OpenCV_LIBS}
        ${EIGEN3_LIBS}
        ${Pangolin_LIBRARIES}
        ${PROJECT_SOURCE_DIR}/../../../Thirdparty/DBoW2/lib/libDBoW2.so
        ${PROJECT_SOURCE_DIR}/../../../Thirdparty/g2o/lib/libg2o.so
        ${PROJECT_SOURCE_DIR}/../../../lib/libORB_SLAM3.so
        -lboost_system
        )

# Node for monocular camera
rosbuild_add_executable(Mono
        src/ros_mono.cc
        )

target_link_libraries(Mono
        ${LIBS}
        )

# Node for monocular camera (Augmented Reality Demo)
rosbuild_add_executable(MonoAR
        src/AR/ros_mono_ar.cc
        src/AR/ViewerAR.h
        src/AR/ViewerAR.cc
        )

target_link_libraries(MonoAR
        ${LIBS}
        )

# Node for stereo camera
rosbuild_add_executable(Stereo
        src/ros_stereo.cc
        )

target_link_libraries(Stereo
        ${LIBS}
        )

# Node for RGB-D camera
rosbuild_add_executable(RGBD
        src/ros_rgbd.cc
        )

target_link_libraries(RGBD
        ${LIBS}
        )

# Node for monocular-inertial camera
rosbuild_add_executable(Mono_Inertial
        src/ros_mono_inertial.cc
        )

target_link_libraries(Mono_Inertial
        ${LIBS}
        )

# Node for stereo-inertial camera
rosbuild_add_executable(Stereo_Inertial
        src/ros_stereo_inertial.cc
        )

target_link_libraries(Stereo_Inertial
        ${LIBS}
        )
