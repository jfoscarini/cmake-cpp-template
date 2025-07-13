cmake_minimum_required(VERSION 3.27)
project(Test)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/../cmake-modules)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_compile_options(-std=gnu++2b -Wall -Wextra -Wdouble-promotion -Wno-unused-parameter -Wno-unused-function -Wno-sign-conversion)
include_directories(../include ../lib)

find_package(Catch2 3 REQUIRED)

add_executable(sample sample.cpp)
target_link_libraries(sample PRIVATE Catch2::Catch2WithMain)
