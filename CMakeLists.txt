cmake_minimum_required(VERSION 2.8.3)
project(c2_pilot_ddp)

set(CMAKE_CXX_FLAGS "-std=c++0x ${CMAKE_CXX_FLAGS}")
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  c2_ros REQUIRED
  c2_ros_msgs REQUIRED
  asco_utils REQUIRED
  roscpp
  rospy
  std_msgs
  message_generation
  geometry_msgs
  tf
  tf2_ros
  visualization_msgs
  mavros
  dynamic_reconfigure
  gcop_comm
  enu
)

find_package(OpenCV REQUIRED)
find_package(GCOP REQUIRED algos systems views est utils)

find_package(TinyXML REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(GLU REQUIRED)
find_package(GLUT REQUIRED)
find_package(OpenGL REQUIRED)

find_library(SWIFTNAV swiftnav)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend tag for "message_generation"
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependency has been pulled in
##     but can be declared for certainty nonetheless:
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
 add_message_files(
   	FILES
   	Ctrl.msg
	State.msg
	CtrlTraj.msg
 )
 
 generate_dynamic_reconfigure_options(
  cfg/configg.cfg
  cfg/mavros_ctrl.cfg
)

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
 generate_messages(
   DEPENDENCIES
   c2_ros_msgs
   std_msgs
   geometry_msgs 
   visualization_msgs
 )

################################################
## Declare ROS dynamic reconfigure parameters ##
################################################

## To declare and build dynamic reconfigure parameters within this
## package, follow these steps:
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for "dynamic_reconfigure"
## * In this file (CMakeLists.txt):
##   * add "dynamic_reconfigure" to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * uncomment the "generate_dynamic_reconfigure_options" section below
##     and list every .cfg file to be processed


###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES c2_pilot_ddp
#  CATKIN_DEPENDS c2_ros roscpp rospy std_msgs
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${mavros_INCLUDE_DIRS}
  ${GCOP_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
  ${c2_ros_INCLUDE_DIR}
)

## Declare a C++ library
# add_library(c2_pilot_ddp
#   src/pilot_ddp.cpp
# )

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(c2_pilot_ddp ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
# add_executable(c2_pilot_ddp_node src/c2_pilot_ddp_node.cpp)

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(c2_pilot_ddp_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
# target_link_libraries(c2_pilot_ddp_node
#   ${catkin_LIBRARIES}
# )

## Declare a cpp executable
add_executable(pilot_ddp src/pilot_ddp.cpp)
add_executable(pilot_ddp2 src/pilot_ddp2.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
add_dependencies(pilot_ddp ${PROJECT_NAME}_gencfg gcop_comm_generate_messages_cpp c2_ros_msgs_gencpp ${catkin_EXPORTED_TARGETS})
add_dependencies(pilot_ddp2 ${PROJECT_NAME}_gencfg gcop_comm_generate_messages_cpp c2_ros_msgs_gencpp ${catkin_EXPORTED_TARGETS})


## Specify libraries to link a library or executable target against
target_link_libraries(pilot_ddp 
			${c2_ros_LIBRARIES}		     
			${GCOP_LIBRARIES}
		    ${OpenCV_LIBRARIES}
	      	${TINYXML_LIBRARIES}
	      	${catkin_LIBRARIES}
	       	${mavros_LIBRARIES} 
	       )
target_link_libraries(pilot_ddp2
			${c2_ros_LIBRARIES} 		     
			${GCOP_LIBRARIES}
		    ${OpenCV_LIBRARIES}
      	    ${TINYXML_LIBRARIES}
	      	${catkin_LIBRARIES}
	       	${mavros_LIBRARIES} 
	       )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS c2_pilot_ddp c2_pilot_ddp_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )





#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_c2_pilot_ddp.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
