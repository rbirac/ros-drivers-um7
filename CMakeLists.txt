cmake_minimum_required(VERSION 2.8.3)
project(um7)

find_package(catkin REQUIRED COMPONENTS roscpp serial sensor_msgs message_generation)

add_service_files(
  FILES
  Reset.srv
)

generate_messages()

catkin_package(
   INCLUDE_DIRS include
   CATKIN_DEPENDS message_runtime
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include
  ${catkin_INCLUDE_DIRS}
)

## Declare a cpp executable
add_executable(um7_driver src/main.cpp src/registers.cpp src/comms.cpp)
target_link_libraries(um7_driver ${catkin_LIBRARIES} )
add_dependencies(um7_driver um7_generate_messages_cpp)


#############
## Install ##
#############

install(TARGETS um7_driver
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

##########
## Docs ##
##########

add_custom_target (${PROJECT_NAME}_doc 
  rosdoc_lite -o ${CATKIN_DEVEL_PREFIX}/doc/${PROJECT_NAME} ${CATKIN_DEVEL_PREFIX}/../src/${PROJECT_NAME}
)

FILE(GLOB LINT_SRCS src/*.cpp include/um7/*.h)
FILE(GLOB NOLINT_SRCS include/um7/firmware_registers.h)
LIST(REMOVE_ITEM LINT_SRCS ${NOLINT_SRCS})
add_custom_target(${PROJECT_NAME}_cpplint
  COMMAND cpplint --filter=-whitespace/line_length ${LINT_SRCS})

#############
## Testing ##
#############


