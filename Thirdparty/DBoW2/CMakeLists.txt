cmake_minimum_required(VERSION 3.16)
project(DBoW2)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

find_package(OpenCV 4.2 REQUIRED)

add_library(${PROJECT_NAME}
  DBoW2/BowVector.cpp
  DBoW2/FORB.cpp
  DBoW2/FeatureVector.cpp
  DBoW2/ScoringObject.cpp
  DUtils/Random.cpp
  DUtils/Timestamp.cpp)

target_include_directories(${PROJECT_NAME} PUBLIC DBoW2 DUtils ${OpenCV_INCLUDE_DIRS})

target_link_libraries(${PROJECT_NAME} PUBLIC ${OpenCV_LIBS})

set_target_properties(${PROJECT_NAME} PROPERTIES
  CXX_STANDARD 14)

target_compile_options(${PROJECT_NAME} PRIVATE -Wall -O3 -march=native)