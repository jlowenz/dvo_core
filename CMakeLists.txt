cmake_minimum_required(VERSION 2.4.6)
project(dvo_core)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

find_package(catkin COMPONENTS
  sophus)
include_directories(${catkin_INCLUDE_DIRS})

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#common commands for building c++ executables and libraries
#rosbuild_add_library(${PROJECT_NAME} src/example.cpp)
#target_link_libraries(${PROJECT_NAME} another_library)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)
#rosbuild_add_executable(example examples/example.cpp)
#target_link_libraries(example ${PROJECT_NAME})

# Eigen3 library
find_package(Eigen3 REQUIRED)
include_directories(${Eigen3_INCLUDE_DIRS})

# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})

# OpenCV
find_package(OpenCV 3 REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

find_package(PCL REQUIRED common filters visualization)
include_directories(${PCL_INCLUDE_DIRS})
add_definitions(${PCL_DEFINITIONS})

#find_package(TBB REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(TBB REQUIRED tbb)
include_directories(${TBB_INCLUDEDIR})
add_definitions(${TBB_DEFINITIONS})

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES dvo_core
  CATKIN_DEPENDS sophus)
  

# SSE flags
#rosbuild_check_for_sse()

include_directories(include)

set(CMAKE_CXX_FLAGS_RELWITHDEBINFO
   "${SSE_FLAGS} -O3 -march=native"
)

add_library(${PROJECT_NAME} 
  src/core/interpolation.cpp
  src/core/intrinsic_matrix.cpp
  src/core/least_squares.cpp
  src/core/math_sse.cpp
  src/core/rgbd_image.cpp
  src/core/rgbd_image_sse.cpp
  src/core/surface_pyramid.cpp
  src/core/weight_calculation.cpp
  
  src/util/histogram.cpp
  
  src/visualization/async_point_cloud_builder.cpp
  src/visualization/point_cloud_aggregator.cpp
  src/visualization/visualizer.cpp
  src/visualization/camera_trajectory_visualizer.cpp
  src/visualization/pcl_camera_trajetory_visualizer.cpp
  
  src/dense_tracking.cpp
  src/dense_tracking_config.cpp)

target_link_libraries(${PROJECT_NAME} 
    ${TBB_LIBRARIES}
    boost_thread
    ${catkin_LIBRARIES}
    ${PCL_LIBRARIES})
