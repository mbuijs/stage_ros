cmake_minimum_required(VERSION 3.10)
project(stage_ros)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(Boost REQUIRED COMPONENTS system thread)
find_package(stage REQUIRED)

set(dependencies
    geometry_msgs
    nav_msgs
    rclcpp
    sensor_msgs
    std_msgs
    std_srvs
    tf2
    tf2_ros
    tf2_geometry_msgs
)

find_package(ament_cmake REQUIRED)
foreach(dep ${dependencies})
    find_package(${dep} REQUIRED)
endforeach(dep)


include_directories(
    ${Boost_INCLUDE_DIRS}
    ${STAGE_INCLUDE_DIRS}
)

# Declare a cpp executable
add_executable(stageros src/stageros.cpp)
set(${PROJECT_NAME}_extra_libs "")
if(UNIX AND NOT APPLE)
    set(${PROJECT_NAME}_extra_libs dl)
endif()
target_link_libraries(stageros
    ${Boost_LIBRARIES}
    ${STAGE_LIBRARIES}
    ${${PROJECT_NAME}_extra_libs}
)
ament_target_dependencies(stageros
    ${dependencies})


## Install

install(TARGETS stageros
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION lib/${PROJECT_NAME}
)

install(PROGRAMS scripts/upgrade-world.sh
    DESTINATION share/${PROJECT_NAME})

install(DIRECTORY rviz
    DESTINATION share/${PROJECT_NAME})

install(DIRECTORY world
    DESTINATION share/${PROJECT_NAME})

## Tests

#if(CATKIN_ENABLE_TESTING)
#  find_package(rostest REQUIRED)
#  add_rostest(test/hztest.xml)
#  add_rostest(test/intensity_test.xml)
#endif()

ament_package()
