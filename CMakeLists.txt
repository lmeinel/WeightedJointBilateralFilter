cmake_minimum_required(VERSION 2.8.10)
project(DepthMapRefinement)

#OpenCV components
set(OPENCV_COMPONENT_LIST
	opencv_core 
	opencv_highgui 
	opencv_imgproc 
	opencv_contrib 
	opencv_calib3d
	opencv_flann
	opencv_features2d
	opencv_ml
	opencv_video
	opencv_objdetect)

find_package(OpenCV COMPONENTS ${OPENCV_COMPONENT_LIST} REQUIRED)


file(GLOB_RECURSE SRCS ${CMAKE_CURRENT_LIST_DIR}/*.cpp)
file(GLOB_RECURSE HDRS ${CMAKE_CURRENT_LIST_DIR}/*.h)

add_executable(${PROJECT_NAME} ${SRCS} ${HDRS})

target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})

if(OPENCV_COMPONENT_LIST)
	foreach(OPENCV_COMPONENT ${OPENCV_COMPONENT_LIST})
		 list(APPEND COPY_FILE_LIST
			 $<TARGET_FILE:${OPENCV_COMPONENT}>)
	endforeach()
endif()

foreach(COPY_FILE ${COPY_FILE_LIST})
	add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_if_different ${COPY_FILE} $<TARGET_FILE_DIR:${PROJECT_NAME}>)
endforeach()

add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}/dataset $<TARGET_FILE_DIR:${PROJECT_NAME}>/dataset)