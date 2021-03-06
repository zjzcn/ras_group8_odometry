cmake_minimum_required(VERSION 2.8.3)
project(ras_group8_odometry)

find_package(catkin REQUIRED
  COMPONENTS
  roscpp
  geometry_msgs
  tf
  nav_msgs
)

catkin_package(
  INCLUDE_DIRS
    include

  CATKIN_DEPENDS
    roscpp
    geometry_msgs
    tf
    nav_msgs

# DEPENDS
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME}_twist_msg_buffer
  src/TwistMsgBuffer.cpp
)

add_executable(${PROJECT_NAME}
  src/odometry_node.cpp
  src/Odometry.cpp
)

add_dependencies(${PROJECT_NAME}
  ${catkin_EXPORTED_TARGETS}
)

target_link_libraries(${PROJECT_NAME}_twist_msg_buffer
  ${catkin_LIBRARIES}
)

target_link_libraries(${PROJECT_NAME}
  ${PROJECT_NAME}_twist_msg_buffer
  ${catkin_LIBRARIES}
)

if (CATKIN_ENABLE_TESTING)
  catkin_add_gtest(${PROJECT_NAME}_tests
    test/test_odometry.cpp
    src/Odometry.cpp
  )
  
  target_link_libraries(${PROJECT_NAME}_tests
    ${PROJECT_NAME}_twist_msg_buffer
    ${catkin_LIBRARIES}
  )

  catkin_add_gtest(test_twist_msg_buffer
    test/test_twist_msg_buffer.cpp
  )
  
  target_link_libraries(test_twist_msg_buffer
    ${PROJECT_NAME}_twist_msg_buffer
    ${catkin_LIBRARIES}
  )
endif()

# Make main executable
install(
  TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# Install headers
install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.hpp"
)