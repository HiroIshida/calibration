cmake_minimum_required(VERSION 3.0.2)
project(image_cb_detector)

find_package(Boost REQUIRED thread)
find_package(catkin REQUIRED actionlib actionlib_msgs calibration_msgs cv_bridge genmsg geometry_msgs image_transport message_filters roscpp sensor_msgs std_msgs)
find_package(OpenCV REQUIRED)

catkin_python_setup()

# generate the messages
add_action_files(DIRECTORY action FILES Config.action)

add_message_files(DIRECTORY msg
                  FILES ImagePoint.msg
                        ObjectInImage.msg
)

generate_messages(DEPENDENCIES actionlib_msgs geometry_msgs std_msgs)

# define the package
catkin_package(INCLUDE_DIRS include
               LIBRARIES ${PROJECT_NAME}
)

# define some includes
include_directories(include)
include_directories(SYSTEM ${Boost_INCLUDE_DIRS}
                           ${catkin_INCLUDE_DIRS}
                           ${OpenCV_INCLUDE_DIRS}
)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/image_cb_detector/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
 
# add the library
add_library(${PROJECT_NAME} src/image_cb_detector.cpp)
target_link_libraries(image_cb_detector ${catkin_LIBRARIES}
                                        ${OpenCV_LIBRARIES}
)
add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS})
install(TARGETS ${PROJECT_NAME}
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

# add some executables
add_executable(image_cb_detector_action src/image_cb_detector_action.cpp)
target_link_libraries(image_cb_detector_action ${Boost_LIBRARIES}
                                               ${catkin_LIBRARIES}
                                               image_cb_detector
                                               ${OpenCV_LIBRARIES}
)
add_dependencies(image_cb_detector_action ${catkin_EXPORTED_TARGETS})

add_executable(rgbd_cb_detector_action 
src/rgbd_cb_detector_action.cpp
src/depth_to_pointcloud.cpp)
target_link_libraries(rgbd_cb_detector_action ${Boost_LIBRARIES}
                                              ${catkin_LIBRARIES}
                                              image_cb_detector
                                              ${OpenCV_LIBRARIES}
)
add_dependencies(rgbd_cb_detector_action ${catkin_EXPORTED_TARGETS})

add_executable(image_annotator src/image_annotator.cpp)
target_link_libraries(image_annotator image_cb_detector ${OpenCV_LIBRARIES})
target_link_libraries(image_annotator ${Boost_LIBRARIES}
                                      ${catkin_LIBRARIES}
                                      image_cb_detector
                                      ${OpenCV_LIBRARIES}
)
add_dependencies(image_annotator ${catkin_EXPORTED_TARGETS})

install(TARGETS image_cb_detector_action rgbd_cb_detector_action image_annotator
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
