cmake_minimum_required (VERSION 3.7)

project (Meld)
add_executable(Meld main.cpp)

# extra cmake modules
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")

# lunar g sdk is needed for validation layers and shader comiplation
set(LUNARG_SDK_PATH "/home/fynch/.opt/VulkanSDK/1.0.54.0/x86_64" CACHE
    FILEPATH "path to the LunarG VulkanSDK")
include_directories("${LUNARG_SDK_PATH}/include")

# package config required for glfw3
find_package(PkgConfig REQUIRED)
pkg_search_module(GLFW REQUIRED glfw3)

include_directories(${GLFW_INCLUDE_DIRS})
target_link_libraries(Meld ${GLFW_STATIC_LIBRARIES})

find_package(JPEG REQUIRED)
target_link_libraries(Meld ${JPEG_LIBRARIES})

find_package(Boost REQUIRED)
include_directories(${Boost_INCLUDE_DIR})

find_package(Vulkan REQUIRED)
include_directories(${Vulkan_INCLUDE_DIRS})
target_link_libraries(Meld ${Vulkan_LIBRARIES})

find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIR})

include_directories("${CMAKE_SOURCE_DIR}/libraries/tinyobjloader/")

# project needs c++11 standards
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

# extra configuration for shader compilation and run script
configure_file (
    "${CMAKE_SOURCE_DIR}/compile_shaders.sh.in"
    "${CMAKE_SOURCE_DIR}/compile_shaders.sh")

configure_file (
    "${CMAKE_SOURCE_DIR}/run.sh.in"
    "${CMAKE_SOURCE_DIR}/run.sh")

set_directory_properties(PROPERTY ADDITIONAL_MAKE_CLEAN_FILES
    "${CMAKE_SOURCE_DIR}/compile_shaders.sh;${CMAKE_SOURCE_DIR}/run.sh;")
