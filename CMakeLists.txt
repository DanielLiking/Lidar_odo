cmake_minimum_required(VERSION 3.0.2)
project(2DLIO)

## Compile as C++11, supported in ROS Kinetic and newer
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++14")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g -pthread")



# List C++ dependencies on ros packages
find_package(catkin REQUIRED COMPONENTS
    tf
    tf2
    tf2_geometry_msgs
    tf2_ros
    roscpp
    rospy
    roslib
    # msg
    std_msgs
    sensor_msgs
    geometry_msgs
    nav_msgs
    # cv
    cv_bridge
    # pcl
    pcl_conversions
    # msg generation
    #message_generation
)




find_package(OpenMP REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Ceres REQUIRED)
find_package(GTSAM REQUIRED QUIET)
find_package(Boost REQUIRED COMPONENTS filesystem program_options system timer)
find_package(PkgConfig)
pkg_check_modules(csm REQUIRED csm)


## Generate messages in the 'msg' folder

catkin_package(
 INCLUDE_DIRS include
 LIBRARIES 2DLIO
 DEPENDS PCL GTSAM
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
    ${catkin_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
    ${CERES_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${GTSAM_INCLUDE_DIR}
    ${csm_INCLUDE_DIRS}
)

link_directories(
    ${PCL_LIBRARY_DIRS}
    ${OpenCV_LIBRARY_DIRS}
    ${GTSAM_LIBRARY_DIRS}
    ${csm_LIBRARY_DIRS}
    )



file(GLOB visual_feature_files
    "src/visual_odometry/visual_feature/*.cpp"
    "src/visual_odometry/visual_feature/camera_models/*.cc"
)
file(GLOB visual_odometry_files
    "src/visual_odometry/visual_estimator/*.cpp"
    "src/visual_odometry/visual_estimator/factor/*.cpp"
    "src/visual_odometry/visual_estimator/initial/*.cpp"
    "src/visual_odometry/visual_estimator/utility/*.cpp"
)
file(GLOB visual_loop_files
    "src/visual_odometry/visual_loop/*.cpp"
    "src/visual_odometry/visual_loop/utility/*.cpp"
    "src/visual_odometry/visual_loop/ThirdParty/*.cpp"
    "src/visual_odometry/visual_loop/ThirdParty/DBoW/*.cpp"
    "src/visual_odometry/visual_loop/ThirdParty/DUtils/*.cpp"
    "src/visual_odometry/visual_loop/ThirdParty/DVision/*.cpp"
    "src/visual_odometry/visual_feature/camera_models/*.cc"
)    

add_executable(${PROJECT_NAME}_visual_feature ${visual_feature_files})
target_link_libraries(${PROJECT_NAME}_visual_feature ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS} ${CERES_LIBRARIES})
######################
### lidar odometry
######################
add_executable(${PROJECT_NAME}_scan_match_plicp_node src/lidar_odometry/scan_match_plicp.cc)

target_link_libraries( ${PROJECT_NAME}_scan_match_plicp_node 
${catkin_LIBRARIES} 
${csm_LIBRARIES}
)

add_dependencies(${PROJECT_NAME}_scan_match_plicp_node 
${csm_EXPORTED_TARGETS} 
${catkin_EXPORTED_TARGETS}
)

# lesson3_plicp_odometry_node
add_executable(${PROJECT_NAME}_plicp_odometry_node src/lidar_odometry/plicp_odometry.cc)

target_link_libraries( ${PROJECT_NAME}_plicp_odometry_node 
${catkin_LIBRARIES} 
${csm_LIBRARIES}
)

add_dependencies(${PROJECT_NAME}_plicp_odometry_node 
${csm_EXPORTED_TARGETS} 
${catkin_EXPORTED_TARGETS}
)

add_dependencies(${PROJECT_NAME}_plicp_odometry_node 
${csm_EXPORTED_TARGETS} 
${catkin_EXPORTED_TARGETS}
)