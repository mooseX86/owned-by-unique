cmake_minimum_required(VERSION 3.2.2)
project("owned_pointer" CXX)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall")
set(GMOCK_INCLUDE_DIR "${CMAKE_SOURCE_DIR}/google-test/googlemock/include")
set(GTEST_INCLUDE_DIR "${CMAKE_SOURCE_DIR}/google-test/googletest/include")

if("${BUILD_SHARED_LIBS}" STREQUAL "ON")
  string(REPLACE "/MTd" "/MDd" CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG}")
  string(REPLACE "/MT" "/MD" CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE}")
else()
  string(REPLACE "/MDd" "/MTd" CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG}")
  string(REPLACE "/MD" "/MT" CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE}")
endif()

if(${WIN32})
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /D not=! /D and=&& /D or=||")
endif()

if(${ENABLE_COVERAGE})
  add_compile_options(--coverage -O0)
  link_libraries(--coverage)
endif()

enable_testing()
add_subdirectory(google-test/)

add_library(owned_pointer INTERFACE)
add_executable(owned_pointer_ut ./ut/owned_pointer_ut.cpp)

target_include_directories(owned_pointer INTERFACE inc/)
target_include_directories(owned_pointer_ut SYSTEM PRIVATE ${GMOCK_INCLUDE_DIR} ${GTEST_INCLUDE_DIR})
target_link_libraries(owned_pointer_ut PRIVATE owned_pointer gmock_main)

add_test(onwed_pointer_ut ${CMAKE_BINARY_DIR}/owned_pointer_ut --gtest_color=yes)
