cmake_minimum_required(VERSION 3.2)
project(apriltags VERSION 3.0.0 DESCRIPTION "AprilTag 3 implementation")

#find_package(PCL 1.7 REQUIRED)

add_executable(apriltag_demo apriltag_demo.c utils/getopt.c)
target_link_libraries(apriltag_demo apriltag)

find_package(OpenCV REQUIRED)

add_executable(opencv_demo opencv_demo.cc utils/getopt.c)
target_link_libraries(opencv_demo apriltag ${OpenCV_LIBS})
