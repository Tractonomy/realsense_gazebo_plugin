################################################################################
# Set minimum required version of cmake, project name and compile options
################################################################################
cmake_minimum_required(VERSION 3.5)
project(realsense_gazebo_plugin)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

################################################################################
# Find ament packages and libraries for ament and system dependencies
################################################################################
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(gazebo_ros REQUIRED)
find_package(image_transport)
find_package(camera_info_manager REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(gazebo REQUIRED)
find_package(urdf REQUIRED)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GAZEBO_CXX_FLAGS}")

include_directories(include ${GAZEBO_INCLUDE_DIRS})
link_directories(${GAZEBO_LIBRARY_DIRS})

add_library(
    ${PROJECT_NAME} SHARED
    src/RealSensePlugin.cpp
    src/gazebo_ros_realsense.cpp
    )

target_link_libraries(${PROJECT_NAME} ${GAZEBO_LIBRARIES})
ament_target_dependencies(${PROJECT_NAME}
  "sensor_msgs"
  "camera_info_manager"
  "image_transport"
  "gazebo_ros"
  "rclcpp"
  "rcl"
)
ament_export_libraries(${PROJECT_NAME})

install(DIRECTORY include/
        DESTINATION include)

install(
    TARGETS ${PROJECT_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(
  DIRECTORY meshes urdf
  DESTINATION share/${PROJECT_NAME}
)

install(
  FILES model.config
  DESTINATION share/${PROJECT_NAME}
)

ament_environment_hooks("${CMAKE_CURRENT_SOURCE_DIR}/env-hooks/${PROJECT_NAME}.dsv.in")
ament_package()
