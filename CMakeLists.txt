cmake_minimum_required(VERSION 2.8.3)
project(ndt_omp)


# pcl 1.7 causes a segfault when it is built with debug mode
set(CMAKE_BUILD_TYPE "RELEASE")


find_package(PCL 1.7 REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

message(STATUS "PCL_INCLUDE_DIRS:" ${PCL_INCLUDE_DIRS})
message(STATUS "PCL_LIBRARY_DIRS:" ${PCL_LIBRARY_DIRS})
message(STATUS "PCL_DEFINITIONS:" ${PCL_DEFINITIONS})

find_package(OpenMP)
if (OPENMP_FOUND)
    MESSAGE("OpenMP found")
    set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

if(MSVC)
    SET(CMAKE_C_FLAGS_RELEASE "/arch:SSE /arch:SSE2 /arch:AVX")
else(MSVC)
    SET(CMAKE_C_FLAGS_RELEASE "--std=c++11 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2 -ffast-math")
    SET(CMAKE_CXX_FLAGS_RELEASE "--std=c++11 -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2 -ffast-math")
endif(MSVC)



###########
## Build ##
###########
include_directories(include)

add_library(ndt_omp
  src/pclomp/voxel_grid_covariance_omp.cpp
  src/pclomp/ndt_omp.cpp
  src/pclomp/gicp_omp.cpp
)

add_executable(align
  apps/align.cpp
)
add_dependencies(align
  ndt_omp
)
target_link_libraries(align
  ${PCL_LIBRARIES}
  ndt_omp
)
