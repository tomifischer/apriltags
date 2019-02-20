cmake_minimum_required(VERSION 3.2)
project(apriltags VERSION 3.0.0 DESCRIPTION "AprilTag 3 implementation")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -fPIC -Wall -Wno-unused-parameter -Wno-unused-function")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -I. -O3 -fno-strict-overflow")

if ( NOT DEFINED CMAKE_INSTALL_LIBDIR )
  set( CMAKE_INSTALL_LIBDIR lib )
endif ( )

if ( NOT DEFINED CMAKE_INSTALL_INCLUDEDIR )
  set( CMAKE_INSTALL_INCLUDEDIR include )
endif ( )

if ( NOT DEFINED CMAKE_INSTALL_DATAROOTDIR )
  set( CMAKE_INSTALL_DATAROOTDIR share )
endif ( )

file(GLOB APRILTAG_SOURCES src/*.c src/common/*.c)

# Build a shared library.
add_library(apriltags SHARED ${APRILTAG_SOURCES})
target_include_directories(apriltags PUBLIC include/apriltags)
target_include_directories(apriltags PRIVATE include/apriltags/common)
#target_include_directories(apriltags PRIVATE src)
target_link_libraries(apriltags m pthread)
set_target_properties(apriltags PROPERTIES
  VERSION ${PROJECT_VERSION} # creates a library file named libX.so.MAJOR.MINOR.PATCH and a symlink named libX.so to it
  SOVERSION ${PROJECT_VERSION_MAJOR} # creates a symlink named libX.so.MAJOR to libX.so.MAJOR.MINOR.PATCH
  # PUBLIC_HEADER "include/apriltags"
)

# Build a static library.
add_library(apriltags_static STATIC ${APRILTAG_SOURCES})
target_include_directories(apriltags_static PUBLIC include/apriltags) # requires 2.8.10 < cmake version
target_include_directories(apriltags_static PRIVATE include/apriltags/common)
#target_include_directories(apriltags_static PRIVATE src)
target_link_libraries(apriltags_static m pthread)
set_target_properties(apriltags_static PROPERTIES OUTPUT_NAME apriltags)

# Populate pkg-config file
configure_file(apriltags.pc.in apriltags.pc @ONLY)

# Build examples.
option(BUILD_EXAMPLES "Build examples." ON)
if(BUILD_EXAMPLES)
  add_subdirectory( example )
endif()

# Install

# If “make install” is invoked or INSTALL is built, CMAKE_INSTALL_PREFIX is
# prepended onto all install directories. This variable defaults to /usr/local
# on UNIX and c:/Program Files on Windows.

# Install library files
install(TARGETS apriltags apriltags_static
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

# Install pkg-config file
install(FILES ${CMAKE_BINARY_DIR}/apriltags.pc
    DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/pkgconfig)
