cmake_minimum_required(VERSION 3.11)
project(stb-cmake C)

set(STB_LIB_NAMES "stb_image;stb_image_write" CACHE STRING "stb libraries included")

include(FetchContent)
FetchContent_Declare(stb-git GIT_REPOSITORY https://github.com/nothings/stb.git)
FetchContent_Populate(stb-git)

set(STB_REPO "${stb-git_SOURCE_DIR}")
set(STB_IMPL_TEMPLATE "stb_impl.c.in")

function(add_stb_library LIB_NAME)
    set(LIB_IMPL "${CMAKE_CURRENT_BINARY_DIR}/${LIB_NAME}.c")
    set(LIB_HEADER "${STB_REPO}/${LIB_NAME}.h")
    string(TOUPPER "${LIB_NAME}_IMPLEMENTATION" IMPL_MACRO)
    configure_file("${STB_IMPL_TEMPLATE}" "${LIB_IMPL}")
    add_library(${LIB_NAME} ${LIB_IMPL} ${LIB_HEADER})
    target_include_directories(${LIB_NAME} PUBLIC "${STB_REPO}")
endfunction()

foreach(LIB_NAME ${STB_LIB_NAMES})
    option(ENABLE_${LIB_NAME} "Enable ${LIB_NAME}" true)
    if (${ENABLE_${LIB_NAME}})
        add_stb_library(${LIB_NAME})
    endif ()
endforeach()
