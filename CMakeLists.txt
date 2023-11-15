cmake_minimum_required(VERSION 3.27)

project(metal_test)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Werror")

find_library(APPLE_FWK_FOUNDATION Foundation REQUIRED)
find_library(APPLE_FWK_QUARTZ_CORE QuartzCore REQUIRED)
find_library(APPLE_FWK_METAL Metal REQUIRED)

add_executable(metal_test
  vector_addition/main.cpp
  vector_addition/metal_adder.cpp
  vector_addition/metal_adder.hpp
  ${CMAKE_CURRENT_BINARY_DIR}/add.air
  ${CMAKE_CURRENT_BINARY_DIR}/add.metallib
)

add_custom_command(OUTPUT add.air
  MAIN_DEPENDENCY vector_addition/add.metal
  COMMAND xcrun -sdk macosx metal -c ${CMAKE_CURRENT_SOURCE_DIR}/vector_addition/add.metal -o ${CMAKE_CURRENT_BINARY_DIR}/add.air)

add_custom_command(OUTPUT add.metallib
  MAIN_DEPENDENCY add.air
  COMMAND xcrun -sdk macosx metallib ${CMAKE_CURRENT_BINARY_DIR}/add.air -o ${CMAKE_CURRENT_BINARY_DIR}/add.metallib)

target_include_directories(metal_test PUBLIC metal-cpp)

target_link_libraries(metal_test
  ${APPLE_FWK_FOUNDATION}
  ${APPLE_FWK_QUARTZ_CORE}
  ${APPLE_FWK_METAL}
)
