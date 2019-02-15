cmake_minimum_required(VERSION 3.2)
project(apriltags VERSION 3.0.0 DESCRIPTION "AprilTag 3 implementation")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -fPIC -Wall -Wno-unused-parameter -Wno-unused-function")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -I. -O3 -fno-strict-overflow")

file(GLOB APRILTAG_SOURCES src/*.c src/common/*.c)

# Build a shared library.
add_library(apriltag SHARED ${APRILTAG_SOURCES})
target_include_directories(apriltag PUBLIC include/apriltags)
target_include_directories(apriltag PRIVATE include/apriltags/common)
target_include_directories(apriltag PRIVATE src)
target_link_libraries(apriltag m pthread)
set_target_properties(apriltag PROPERTIES
  VERSION ${PROJECT_VERSION} # creates a library file named libX.so.MAJOR.MINOR.PATCH and a symlink named libX.so to it
  SOVERSION ${PROJECT_VERSION_MAJOR} # creates a symlink named libX.so.MAJOR to libX.so.MAJOR.MINOR.PATCH
  # PUBLIC_HEADER "include/apriltags"
)

# Build a static library.
add_library(apriltag_static STATIC ${APRILTAG_SOURCES})
target_include_directories(apriltag_static PUBLIC include/apriltags) # requires 2.8.10 < cmake version
target_include_directories(apriltag_static PRIVATE include/apriltags/common)
target_include_directories(apriltag_static PRIVATE src)
target_link_libraries(apriltag_static m pthread)
set_target_properties(apriltag_static PROPERTIES OUTPUT_NAME apriltag)

# Build examples.
option(BUILD_EXAMPLES "Build examples." ON)
if(BUILD_EXAMPLES)
  add_subdirectory( example )
endif()

# Install

# If “make install” is invoked or INSTALL is built, CMAKE_INSTALL_PREFIX is
# prepended onto all install directories. This variable defaults to /usr/local
# on UNIX and c:/Program Files on Windows.

if ( NOT DEFINED CMAKE_INSTALL_LIBDIR )
  set( CMAKE_INSTALL_LIBDIR lib )
endif ( )

if ( NOT DEFINED CMAKE_INSTALL_INCLUDEDIR )
  set( CMAKE_INSTALL_INCLUDEDIR include )
endif ( )

install(TARGETS apriltag apriltag_static
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} # for dynamic library
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} # for static library
  # PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Using the PUBLIC_HEADER property does not allow installing a directory
# structure, therefore header files will be installed on the side.
INSTALL (
  DIRECTORY include/apriltags
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  FILES_MATCHING PATTERN "*.h*"
)
