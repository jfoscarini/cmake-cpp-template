cmake_minimum_required(VERSION 3.27)

get_filename_component(PROJECT_NAME ${CMAKE_CURRENT_SOURCE_DIR} NAME)
project(${PROJECT_NAME} VERSION 0.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake-modules)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_compile_options(
    -std=gnu++2b
    -Wall
    -Wextra
    -Wdouble-promotion
    -Wsign-conversion
    -Wconversion
    -Wmissing-field-initializers
    -Winit-self
    -Wshadow
    -Wstrict-aliasing=2
    -Wfloat-equal
    -Wno-unused-parameter
    -Wno-unused-function
    # -Werror
)

include_directories(include lib)

add_executable(${PROJECT_NAME}
    src/main.cpp
)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${PROJECT_VERSION})
target_compile_definitions(${PROJECT_NAME} PRIVATE
    PROJECT_NAME="${PROJECT_NAME}"
    PROJECT_VERSION_MAJOR=${PROJECT_VERSION_MAJOR}
    PROJECT_VERSION_MINOR=${PROJECT_VERSION_MINOR}
    PROJECT_VERSION_PATCH=${PROJECT_VERSION_PATCH}
    $<$<CONFIG:Debug>:LOG_LEVEL=7>
    $<$<CONFIG:Release>:LOG_LEVEL=-1 NDEBUG>
)
target_compile_options(${PROJECT_NAME} PRIVATE
    $<$<CONFIG:Debug>:-g3 -O0 -fsanitize=address,undefined -fno-omit-frame-pointer>
)
target_link_options(${PROJECT_NAME} PRIVATE
    $<$<CONFIG:Debug>:-fsanitize=address,undefined>
)

target_link_libraries(${PROJECT_NAME} PRIVATE)
