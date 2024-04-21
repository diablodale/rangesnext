cmake_minimum_required(VERSION 3.29)
project(rangesnext
    HOMEPAGE_URL https://github.com/cor3ntin/rangesnext
)

file(GLOB_RECURSE HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp)
add_library(rangesnext INTERFACE)
target_compile_features(rangesnext INTERFACE cxx_std_20)
target_sources(rangesnext INTERFACE ${HEADERS})
target_include_directories(rangesnext INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include/)

#file(GLOB SRCS test/*.cpp)
set(SRCS test/enumerate.cpp)
add_executable(rangesnext_test EXCLUDE_FROM_ALL ${SRCS})
target_compile_features(rangesnext_test INTERFACE cxx_std_20)

if(RANGESNEXT_CATCH2 STREQUAL "PRIVATE")
    message(STATUS "Using private Catch2 v2")
    add_subdirectory(test/Catch2 EXCLUDE_FROM_ALL)
    target_link_libraries(rangesnext_test rangesnext Catch2)
    target_compile_definitions(rangesnext_test PRIVATE CATCH2V2)
    target_sources(rangesnext_test PRIVATE test/main.cpp)
else()
    find_package(Catch2 CONFIG REQUIRED)
    target_link_libraries(rangesnext_test PRIVATE rangesnext Catch2::Catch2WithMain)
endif()

include(CTest)
add_test(NAME rangesnext COMMAND rangesnext_test)

if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(rangesnext_test PRIVATE -fcoroutines -fconcepts-diagnostics-depth=50 -Wall -Wextra)
endif()
