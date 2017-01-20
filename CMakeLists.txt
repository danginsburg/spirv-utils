cmake_minimum_required(VERSION 2.8)

project(spirv-utils)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
  message(STATUS "Build mode: debug")
else()
  message(STATUS "Build mode: release")
endif (CMAKE_BUILD_TYPE STREQUAL "Debug")

# Export commands for YCM
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

# Set custom cmake modules path
set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules")

# Set default cmake build type
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Debug CACHE STRING
      "Choose the type of build, options are: None Debug Release RelWithDebInfo\
MinSizeRel. Default is Debug."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)

if(MSVC)
  # Force to always compile with W4
  if(CMAKE_CXX_FLAGS_DEBUG MATCHES "/W[0-4]")
    string(REGEX REPLACE "/W[0-4]" "/W4" CMAKE_CXX_FLAGS_DEBUG
           "${CMAKE_CXX_FLAGS_DEBUG}")
  else()
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /W4")
  endif()
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR
   CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  # Update if necessary
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 ")
  set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall")
  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Wall")
endif()

if(UNIX)
  if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    # Color error output
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fcolor-diagnostics")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --std=c++11 -stdlib=libc++")
  endif()
endif()

message(STATUS "Compiler custom flags are: ${CMAKE_CXX_FLAGS}")
message(STATUS "Compiler debug flags are: ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "Compiler release flags are: ${CMAKE_CXX_FLAGS_RELEASE}")

# Set include directories
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

# Set headers and sources
set(SUT_HEADERS
  ${CMAKE_CURRENT_SOURCE_DIR}/include/spv_utils.h)

set(SUT_SOURCES
  ${CMAKE_CURRENT_SOURCE_DIR}/source/spv_utils.cpp)

# Create library
add_library(sut
  ${SUT_HEADERS}
  ${SUT_SOURCES})

option(SUT_BUILD_SAMPLE "Build the sample application" OFF)

if(SUT_BUILD_SAMPLE)
  set(SUT_SAMPLE_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/source/main.cpp)
  
  add_executable(sut_sample
    ${SUT_SAMPLE_SOURCES})

  target_link_libraries(sut_sample
    sut)

endif(SUT_BUILD_SAMPLE)

  



