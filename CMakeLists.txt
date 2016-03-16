project(fluids3)
cmake_minimum_required(VERSION 2.8)

aux_source_directory(common COMMON_SRC_LIST)
aux_source_directory(fluids FLUIDS_SRC_LIST)

set(TINYXML_SRC_LIST
  tinyxml/tinystr.cpp
  tinyxml/tinyxml.cpp
  tinyxml/tinyxmlerror.cpp
  tinyxml/tinyxmlparser.cpp
)

INCLUDE_DIRECTORIES(fluids ${CMAKE_SOURCE_DIR})
INCLUDE_DIRECTORIES(common ${CMAKE_SOURCE_DIR})
INCLUDE_DIRECTORIES(tinyxml ${CMAKE_SOURCE_DIR})

set(CMAKE_CXX_COMPILER g++-4.8)
set(CMAKE_C_COMPILER gcc-4.8)

# CUDA
set(CUDA_TOOLKIT_ROOT_DIR "/usr/local/cuda-7.5")
find_package(CUDA REQUIRED)

set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -gencode arch=compute_30,code=sm_30 -ccbin /usr/bin/gcc-4.8)

set (CUDA_SOURCES
  fluids/fluid_system.cu
  fluids/fluid_system_host.cu
  fluids/fluid_system_kern.cu
  fluids/prefix_sum.cu
  fluids/radixsort.cu
  fluids/radixsort_kernel.cu
)

set (CUDA_HEADERS
  fluids/fluid_system_host.cuh
  fluids/fluid_system_kern.cuh
  fluids/radixsort.cuh
)

cuda_add_library(${PROJECT_NAME}
  ${FLUIDS_SRC_LIST} ${COMMON_SRC_LIST} ${CUDA_SOURCES} ${CUDA_HEADERS}
  OPTIONS -DSTUFF="blah blah"
  RELEASE -DNDEBUG
  DEBUG -g -DDEBUG
)

add_definitions(-DUSE_GLUT -DTIXML_USE_STL)

target_link_libraries(${PROJECT_NAME} GL GLEW cuda)

add_executable(demo main.cpp ${TINYXML_SRC_LIST})
target_link_libraries(demo ${PROJECT_NAME} GL GLEW cuda glut)

