cmake_minimum_required(VERSION 3.5)
project(turtlesim)

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(ament_index_cpp REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(Qt5 REQUIRED COMPONENTS Widgets)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(yaml-cpp REQUIRED)

include_directories(include ${Qt5Widgets_INCLUDE_DIRS})

rosidl_generate_interfaces(${PROJECT_NAME}
  "action/RotateAbsolute.action"
  "msg/Color.msg"
  "msg/Pose.msg"
  "srv/Kill.srv"
  "srv/SetPen.srv"
  "srv/Spawn.srv"
  "srv/TeleportAbsolute.srv"
  "srv/TeleportRelative.srv")

set(dependencies "ament_index_cpp" "geometry_msgs" "rclcpp" "rclcpp_action" "std_msgs" "std_srvs")

set(turtlesim_node_SRCS
  src/turtlesim.cpp
  src/turtle.cpp
  src/turtle_frame.cpp
)
set(turtlesim_node_HDRS
  include/turtlesim/turtle_frame.h
)

qt5_wrap_cpp(turtlesim_node_MOCS ${turtlesim_node_HDRS})


add_executable(turtlesim_node ${turtlesim_node_SRCS} ${turtlesim_node_MOCS})
target_link_libraries(turtlesim_node
  Qt5::Widgets
  yaml-cpp
  ${rclcpp_LIBRARIES}
  ${rosidl_typesupport_cpp_LIBRARIES}
  ${rosidl_typesupport_c_LIBRARIES}
  ${rosidl_typesupport_introspection_cpp_LIBRARIES}
  ${rosidl_typesupport_introspection_c_LIBRARIES}
)
ament_target_dependencies(turtlesim_node ${dependencies})
rosidl_target_interfaces(turtlesim_node ${PROJECT_NAME} "rosidl_typesupport_cpp")

add_executable(turtle_teleop_key tutorials/teleop_turtle_key.cpp)
ament_target_dependencies(turtle_teleop_key ${dependencies})
rosidl_target_interfaces(turtle_teleop_key ${PROJECT_NAME} "rosidl_typesupport_cpp")
add_executable(draw_square tutorials/draw_square.cpp)
ament_target_dependencies(draw_square ${dependencies})
rosidl_target_interfaces(draw_square ${PROJECT_NAME} "rosidl_typesupport_cpp")
add_executable(mimic tutorials/mimic.cpp)
ament_target_dependencies(mimic ${dependencies})
rosidl_target_interfaces(mimic ${PROJECT_NAME} "rosidl_typesupport_cpp")

install(TARGETS turtlesim_node turtle_teleop_key draw_square mimic
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY images
  DESTINATION share/${PROJECT_NAME}
  FILES_MATCHING PATTERN "*.png" PATTERN "*.svg")

install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME})

# Install config file
install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/../config.yaml
  DESTINATION share/${PROJECT_NAME}/config)

ament_package()
