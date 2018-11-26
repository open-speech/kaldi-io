cmake_minimum_required(VERSION 3.0)

project(kaldi_io LANGUAGES C CXX)

set(CMAKE_ROOT_PATH ${CMAKE_CURRENT_SOURCE_DIR})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -I${CMAKE_CURRENT_LIST_DIR} \
    -Wall -Wno-sign-compare -Wno-unused-local-typedefs \
    -Wno-deprecated-declarations -Winit-self -Wno-deprecated-register \
    -Wno-uninitialized \
    -msse -msse2 -pthread \
    -DHAVE_EXECINFO_H=1 -DHAVE_CXXABI_H \
    -D_GLIBCXX_USE_CXX11_ABI=0 \
    -O3")

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/Modules)
set(CMAKE_PREFIX_PATH $ENV{CONDA_PREFIX})
find_package(BLAS REQUIRED)

if (BLAS_FOUND)
    message("using blas lib: ${BLAS_LIBRARIES}")
    MESSAGE(STATUS "Found a library with BLAS API (${BLAS_VENDOR}).")
    if (${BLAS_VENDOR} STREQUAL "ATLAS")
        include_directories(blas/atlas)
        add_definitions(-DHAVE_ATLAS)
    elseif(${BLAS_VENDOR} STREQUAL "MKL")
        include_directories(blas/mkl)
        add_definitions(-DHAVE_MKL)
    elseif(${BLAS_VENDOR} STREQUAL "ACCELERATE")
        add_definitions(-DHAVE_CLAPACK)
    else()
        message( FATAL_ERROR "Cannot find atlas or clapack, CMake will exit." )
    endif ()
else()
    message( FATAL_ERROR "Cannot find blas, CMake will exit." )
endif ()

set(public_headers "kaldi-io.h")

add_subdirectory(base)
add_subdirectory(itf)
add_subdirectory(matrix)
add_subdirectory(util)
add_subdirectory(feat)
add_subdirectory(transform)

# shared and static libraries built from the same object files
add_library(kaldi_io_shared SHARED
        $<TARGET_OBJECTS:base>
        $<TARGET_OBJECTS:matrix>
        $<TARGET_OBJECTS:util>
        $<TARGET_OBJECTS:feat>
        $<TARGET_OBJECTS:transform>)

add_library(kaldi_io_static STATIC
        $<TARGET_OBJECTS:base>
        $<TARGET_OBJECTS:matrix>
        $<TARGET_OBJECTS:util>
        $<TARGET_OBJECTS:feat>
        $<TARGET_OBJECTS:transform>)

target_link_libraries(kaldi_io_shared m pthread dl ${BLAS_LIBRARIES})
target_link_libraries(kaldi_io_static m pthread dl ${BLAS_LIBRARIES})

set_target_properties(kaldi_io_shared PROPERTIES
        PUBLIC_HEADER "${public_headers}")

install(TARGETS kaldi_io_shared kaldi_io_static
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        PUBLIC_HEADER DESTINATION include COMPONENT devel)
