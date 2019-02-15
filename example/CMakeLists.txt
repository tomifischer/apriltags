cmake_minimum_required(VERSION 3.2)
project(apriltags VERSION 3.0.0 DESCRIPTION "AprilTag 3 implementation")

add_executable(apriltag_demo apriltag_demo.c utils/getopt.c)
target_link_libraries(apriltag_demo apriltags)

find_package(OpenCV REQUIRED)

add_executable(opencv_demo opencv_demo.cc utils/getopt.c)
target_link_libraries(opencv_demo apriltags ${OpenCV_LIBS})
