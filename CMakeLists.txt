cmake_minimum_required(VERSION 3.27)

get_filename_component(PROJECT_NAME ${CMAKE_CURRENT_SOURCE_DIR} NAME)
project(${PROJECT_NAME} VERSION 0.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(WARNINGS_AS_ERRORS "Treat warnings as errors" OFF)
option(BUILD_TESTS "Build tests" TRUE)

set(PROJECT_WARNINGS
    -Wall -Wextra -Wdouble-promotion -Wsign-conversion -Wconversion
    -Wmissing-field-initializers -Winit-self -Wshadow -Wstrict-aliasing=2
    -Wfloat-equal -Wno-unused-parameter -Wno-unused-function
)

function(setup_target_defaults TARGET)
    target_include_directories(${TARGET}
        PRIVATE ${CMAKE_SOURCE_DIR}/include
        SYSTEM PUBLIC ${CMAKE_SOURCE_DIR}/external
    )
    target_compile_options(${TARGET} PRIVATE
        $<$<CXX_COMPILER_ID:GNU,Clang>:${PROJECT_WARNINGS}>
        $<$<BOOL:${WARNINGS_AS_ERRORS}>:-Werror>
        $<$<AND:$<CXX_COMPILER_ID:GNU,Clang>,$<CONFIG:Debug>>:-fsanitize=address,undefined -fno-omit-frame-pointer>
    )
    target_link_options(${TARGET} PRIVATE
        $<$<AND:$<CXX_COMPILER_ID:GNU,Clang>,$<CONFIG:Debug>>:-fsanitize=address,undefined>
    )
endfunction()

file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS
    ${CMAKE_SOURCE_DIR}/src/*.cpp
    ${CMAKE_SOURCE_DIR}/src/*.cc
)

add_executable(${PROJECT_NAME} ${SOURCES})
setup_target_defaults(${PROJECT_NAME})

if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)

    add_custom_target(check
        COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure
        DEPENDS ${PROJECT_NAME} tests
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    )
endif()
