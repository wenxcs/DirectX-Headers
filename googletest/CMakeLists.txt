# Copyright (c) Microsoft Corporation.
# Licensed under the MIT License.

# Assume to use vcpkg to install gtest
list(APPEND gtestlibs "")
if("${CMAKE_TOOLCHAIN_FILE}" MATCHES "vcpkg.cmake$")
  find_package(GTest CONFIG REQUIRED)
  list(APPEND gtestlibs GTest::gtest_main)
else()
  include(FetchContent)

  set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
  FetchContent_Declare(
      googletest
      GIT_REPOSITORY https://github.com/google/googletest.git
      GIT_TAG main # Live at head
  )
  FetchContent_MakeAvailable(googletest)
  list(APPEND gtestlibs gtest_main)
endif()

list(APPEND dxlibs "")
if(EXISTS "/dev/dxg" AND EXISTS "/lib/wsl/lib/")
  find_library(libd3d12 d3d12 HINTS /lib/wsl/lib)
  find_library(libdxcore dxcore HINTS /lib/wsl/lib)
  list(APPEND dxlibs ${libd3d12} ${libdxcore})
else()
# Fallback to default: let CMake to looking for libs
  list(APPEND dxlibs d3d12 dxcore)
endif()

project(DirectX-Headers-GoogleTest-Suite CXX)
add_executable(Feature-Support-Test feature_support_test.cpp)
target_link_libraries(Feature-Support-Test DirectX-Headers DirectX-Guids ${dxlibs} ${gtestlibs})