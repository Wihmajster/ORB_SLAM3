cmake_minimum_required(VERSION 3.16)
project(orb_slam3_core)

# Release by default
# Turn on Debug with "-DCMAKE_BUILD_TYPE=Debug"
if(NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE Release)
endif()

find_package(catkin REQUIRED)
find_package(OpenCV 4.2 REQUIRED)
find_package(Eigen3 3.1.0 REQUIRED)

add_definitions(-w)
catkin_package(
        INCLUDE_DIRS
        include
        Thirdparty/DBoW2
        Thirdparty/g2o
        Thirdparty/Sophus
        LIBRARIES orb_slam3 DBoW2 g2o
        CATKIN_DEPENDS roscpp
        DEPENDS EIGEN3 OpenCV)

add_subdirectory(Thirdparty/DBoW2)
add_subdirectory(Thirdparty/g2o)
add_subdirectory(Thirdparty/Sophus)

add_library(orb_slam3
        src/System.cc
        src/Tracking.cc
        src/LocalMapping.cc
        src/LoopClosing.cc
        src/ORBextractor.cc
        src/ORBmatcher.cc
        src/Converter.cc
        src/MapPoint.cc
        src/KeyFrame.cc
        src/Atlas.cc
        src/Map.cc
        src/Optimizer.cc
        src/Frame.cc
        src/KeyFrameDatabase.cc
        src/Sim3Solver.cc
        src/ImuTypes.cc
        src/G2oTypes.cc
        src/CameraModels/Pinhole.cpp
        src/CameraModels/KannalaBrandt8.cpp
        src/OptimizableTypes.cpp
        src/MLPnPsolver.cpp
        src/GeometricTools.cc
        src/TwoViewReconstruction.cc
        src/Config.cc
        src/Settings.cc)

target_include_directories(orb_slam3 PUBLIC
        ${PROJECT_SOURCE_DIR}/include
        ${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2
        ${PROJECT_SOURCE_DIR}/Thirdparty/g2o
        ${PROJECT_SOURCE_DIR}/Thirdparty/Sophus
        ${EIGEN3_INCLUDE_DIR}
        ${catkin_INCLUDE_DIRS}
)

target_link_libraries(orb_slam3 PUBLIC
        ${OpenCV_LIBS}
        ${EIGEN3_LIBS}
        DBoW2
        g2o
        -lboost_serialization
        -lcrypto
)

set_target_properties(orb_slam3 PROPERTIES
        CXX_STANDARD 14)

target_compile_options(orb_slam3 PRIVATE -Wall -O3)

if(CMAKE_BUILD_TYPE EQUAL "Release")
        target_compile_options(orb_slam3 PRIVATE -march=native)
endif()

add_custom_target(vocabulary ALL)
add_custom_command(TARGET vocabulary PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E remove ${CMAKE_BINARY_DIR}/ORBvoc.txt.tar.gz
        COMMAND ${CMAKE_COMMAND} -E tar xf ${PROJECT_SOURCE_DIR}/Vocabulary/ORBvoc.txt.tar.gz
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
        DEPENDS ${PROJECT_SOURCE_DIR}/Vocabulary/ORBvoc.txt.tar.gz
        COMMENT "Unpacking ORBvoc.txt.tar.gz"
        VERBATIM
)
add_dependencies(orb_slam3 vocabulary)