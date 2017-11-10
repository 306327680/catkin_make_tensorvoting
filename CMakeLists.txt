cmake_minimum_required(VERSION 2.8.3)
project(tensor)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
add_definitions(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  pointmatcher_ros
  libpointmatcher
  pcl_ros
)

FIND_PACKAGE(CUDA REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(libnabo REQUIRED)
find_package(libpointmatcher REQUIRED)
find_package(Boost REQUIRED COMPONENTS system)

include_directories( /usr/local/cuda-8.0/samples/common/inc)
include_directories(${Boost_INCLUDE_DIRS})

#SET(CUDA_NVCC_FLAGS "-arch=compute_61" CACHE STRING "nvcc flags" FORCE)
#SET (CUDA_VERBOSE_BUILD ON CACHE BOOL "nvcc verbose" FORCE)
#SET(LIB_TYPE STATIC)


SET(CUDA_NVCC_FLAGS "-arch=sm_61" "-O6" "-use_fast_math")
add_definitions(-DUSE_CUDA)




CUDA_ADD_LIBRARY(CudaVoting ${LIB_TYPE} src/CudaVoting.cu)

catkin_package(

)

include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)
#dense
add_executable(demo_dense_gpu src/demo_dense_gpu.cpp src/tensor_voting.cpp)
add_dependencies(demo_dense_gpu CudaVoting ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(demo_dense_gpu CudaVoting ${POINT_MATCHER_LIBRARY} ${NABO_LIBRARY} ${EXTRA_LIBS} cuda   ${Boost_LIBRARIES} ${catkin_LIBRARIES})

#sparse
add_executable(demo_sparse_gpu src/demo_sparse_gpu.cpp src/tensor_voting.cpp)
add_dependencies(demo_sparse_gpu CudaVoting ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(demo_sparse_gpu CudaVoting ${POINT_MATCHER_LIBRARY} ${NABO_LIBRARY} ${EXTRA_LIBS} cuda   ${Boost_LIBRARIES} ${catkin_LIBRARIES})



