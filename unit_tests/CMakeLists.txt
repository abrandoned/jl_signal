cmake_minimum_required(VERSION 2.6)
project(jl_signal_unit_tests)
set(EXECUTABLE_NAME jl_signal_tests)
set(LIBRARY_NAME js_signal)
set(GTEST_PATH $ENV{GTEST_PATH})
set(GMOCK_PATH $ENV{GMOCK_PATH})

# Use C++ 11
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -v")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -v")

# To use DEBUG
# set(CMAKE_BUILD_TYPE Debug)

IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
   # Mac OS X specific code	
  message(STATUS "Compliling for Darwin")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
  message(STATUS "Compliling for Darwin")
ENDIF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin") 

IF(MSVC)
  # Force to always compile with W4
  IF(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
    string(REGEX REPLACE "/W[0-4]" "/W4" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
  ELSE()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
  ENDIF()
ELSEIF(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXX)
  # Update if necessary
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-long-long -pedantic")
ENDIF()

add_definitions( -DGTEST_USE_OWN_TR1_TUPLE=1 )
add_definitions( -DGTEST_LANG_CXX11=1 )

message(STATUS ${CMAKE_CXX_FLAGS})

include_directories(../src)
include_directories(../../gmock-1.6.0/gtest/include)
include_directories(../../gmock-1.6.0/include)

find_library(
    GTEST_LIBS 
    NAMES 
    gtest 
    HINTS ${GTEST_PATH}
)

find_library(
    GTEST_MAIN_LIBS 
    NAMES 
    gtest_main 
    HINTS ${GTEST_PATH}
)

find_library(
  GMOCK_LIBS
  NAMES
  gmock
  HINTS ${GMOCK_PATH}
)

find_library(
  GMOCK_MAIN_LIBS
  NAMES
  gmock_main
  HINTS ${GMOCK_PATH}
)

find_library(
  PTHREAD
  NAMES
  pthread
)

find_library(
  DL
  NAMES
  dl
)

IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  find_library(
    LIBC++
    NAMES
    stdc++
  )
ENDIF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")

file(
    GLOB_RECURSE
    SRC_FILES
    ../src/*
)

file(
    GLOB_RECURSE
    GTEST_INCLUDE_FILES
    ${GTEST_PATH}/include/*
)

file(
    GLOB_RECURSE
    TEST_SRC_FILES
    src/*
)

add_library(
    ${LIBRARY_NAME}
    ${SRC_FILES}
)

add_executable(
    ${EXECUTABLE_NAME} 
    ${TEST_SRC_FILES}
)

target_link_libraries(
    ${EXECUTABLE_NAME}
    ${LIBRARY_NAME}
    ${GTEST_LIBS}
    ${GTEST_MAIN_LIBS}
    ${GMOCK_LIBS}
    ${GMOCK_MAIN_LIBS}
    ${DL}
    ${LIBC++}
    ${PTHREAD}
)
