cmake_minimum_required(VERSION 2.8.3)
project(ndt_mapping)

add_compile_options(-std=c++11)


find_package(PCL REQUIRED)

IF (NOT (PCL_VERSION VERSION_LESS "1.7.2"))
    SET(PCL_OPENMP_PACKAGES pcl_omp_registration)
ENDIF (NOT (PCL_VERSION VERSION_LESS "1.7.2"))

find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif ()

find_package(Eigen3 REQUIRED)

find_package(catkin REQUIRED COMPONENTS
        roscpp
        std_msgs
        nav_msgs
        tf
        pcl_ros
        sensor_msgs
        pcl_conversions
        velodyne_pointcloud
        ${PCL_OPENMP_PACKAGES}
        )

###################################
## catkin specific configuration ##
###################################
catkin_package(
        #CATKIN_DEPENDS std_msgs velodyne_pointcloud ${PCL_OPENMP_PACKAGES}
        #DEPENDS PCL
)

###########
## Build ##
###########

include_directories(include ${catkin_INCLUDE_DIRS} ${EIGEN3_INCLUDE_DIRS})

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

add_executable(ndt_mapping src/ndt_mapping.cpp)
target_link_libraries(ndt_mapping ${catkin_LIBRARIES})
add_dependencies(ndt_mapping ${catkin_EXPORTED_TARGETS})

if (NOT (PCL_VERSION VERSION_LESS "1.7.2"))
    set_target_properties(ndt_mapping PROPERTIES COMPILE_DEFINITIONS "USE_PCL_OPENMP")
endif (NOT (PCL_VERSION VERSION_LESS "1.7.2"))

