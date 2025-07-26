include(FetchContent)

FetchContent_Declare(
    Catch2
    GIT_REPOSITORY https://github.com/catchorg/Catch2.git
    GIT_TAG v3.9.0
)
FetchContent_MakeAvailable(Catch2)

include(CTest)
include(Catch)

option(WARNINGS_AS_ERRORS "Treat warnings as errors" OFF)

file(GLOB_RECURSE TEST_SOURCES CONFIGURE_DEPENDS
    ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/*.cc
)

function(safe_target_name out_var path)
    file(RELATIVE_PATH rel_path "${CMAKE_CURRENT_SOURCE_DIR}" "${path}")
    string(REPLACE "/" "_" rel_path "${rel_path}")
    string(REGEX REPLACE "\\.[^.]*$" "" rel_path "${rel_path}")
    set(${out_var} "test_${rel_path}" PARENT_SCOPE)
endfunction()

foreach(test_src IN LISTS TEST_SOURCES)
    safe_target_name(test_name "${test_src}")
    add_executable(${test_name} ${test_src})

    if(COMMAND setup_target_defaults)
        setup_target_defaults(${test_name})
    else()
        target_include_directories(${test_name}
            PRIVATE ${CMAKE_SOURCE_DIR}/include
            SYSTEM PUBLIC ${CMAKE_SOURCE_DIR}/external
        )
        target_compile_options(${test_name} PRIVATE
            $<$<CXX_COMPILER_ID:GNU,Clang>:
                -Wall -Wextra -Wdouble-promotion -Wsign-conversion -Wconversion
                -Wmissing-field-initializers -Winit-self -Wshadow -Wstrict-aliasing=2
                -Wfloat-equal -Wno-unused-parameter -Wno-unused-function
            >
            $<$<BOOL:${WARNINGS_AS_ERRORS}>:-Werror>
            $<$<AND:$<CXX_COMPILER_ID:GNU,Clang>,$<CONFIG:Debug>>:-fsanitize=address,undefined -fno-omit-frame-pointer>
        )
        target_link_options(${test_name} PRIVATE
            $<$<AND:$<CXX_COMPILER_ID:GNU,Clang>,$<CONFIG:Debug>>:-fsanitize=address,undefined>
        )
    endif()

    target_link_libraries(${test_name} PRIVATE Catch2::Catch2WithMain)
    catch_discover_tests(${test_name})
endforeach()
