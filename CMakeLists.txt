cmake_minimum_required(VERSION 3.0)

project(KinectFusionLib)
set(PROJECT_VERSION_STRING "1.1.0")
set(PROJECT_TARGET ${PROJECT_NAME})
set(PROJECT_INCLUDE_PREFIX ${PROJECT_NAME})

set(CMAKE_CXX_STANDARD 11)

# Setting output paths
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

# ------------------------------------------------
#                 Dependencies
# ------------------------------------------------
## CUDA
# find_package(CUDA 8.0 REQUIRED)
find_package(CUDA REQUIRED)
IF (CUDA_FOUND)
    include_directories("${CUDA_INCLUDE_DIRS}")
    # Optional: Specify the arch of your CUDA hardware here
    SET(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-O3;-std=c++11 -gencode arch=compute_52,code=sm_52)
ENDIF ()

## OpenCV
# Optional: Set OpenCV_DIR if you want to use a custom version of OpenCV
# SET("OpenCV_DIR" "/opt/opencv/usr/local/share/OpenCV")
find_package(OpenCV REQUIRED)

## Eigen3
find_package(Eigen3 REQUIRED)
# ------------------------------------------------

SET(CUDA_PROPAGATE_HOST_FLAGS OFF)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Weffc++")

# Targets
set(PROJECT_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/include)
set(PROJECT_SOURCE_DIR ${PROJECT_SOURCE_DIR}/src)

include_directories("${PROJECT_INCLUDE_DIR}" "${PROJECT_SOURCE_DIR}/cuda/include")
file(GLOB KinectFusion_SRCS "${PROJECT_SOURCE_DIR}/cuda/*.cu" "${PROJECT_SOURCE_DIR}/*.cpp")

cuda_add_library(KinectFusionLib STATIC "${KinectFusion_SRCS}")
target_link_libraries(KinectFusionLib ${OpenCV_LIBS} Eigen3::Eigen)
target_include_directories(KinectFusionLib PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
        ${CUDA_INCLUDE_DIRS}
        )

set(ALL_INSTALL_TARGETS KinectFusionLib)

include(cmake/installConfig.cmake)
