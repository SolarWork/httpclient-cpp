cmake_minimum_required(VERSION 2.6)

project(TestHTTPClient)
set(EXECUTABLE_OUTPUT_PATH bin/${CMAKE_BUILD_TYPE})
set(CMAKE_CXX_STANDARD 14) # c++14
add_definitions(-DLINUX)

# Code coverage setup
IF(CMAKE_BUILD_TYPE MATCHES Coverage)
   INCLUDE(CodeCoverage.cmake)
   SET(CMAKE_CXX_FLAGS "-g -O0 -fprofile-arcs -ftest-coverage")
   SET(CMAKE_C_FLAGS "-g -O0 -fprofile-arcs -ftest-coverage")
ENDIF(CMAKE_BUILD_TYPE MATCHES Coverage)

# Locate GTest
find_package(GTest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})

# Locate libcURL
find_package(CURL REQUIRED)
include_directories(${CURL_INCLUDE_DIRS})

include_directories(../HTTP)
include_directories(./simpleini)
include_directories(./rapidjson)
include_directories(./)


IF(CMAKE_BUILD_TYPE MATCHES Coverage)

file(GLOB_RECURSE http_source_files ../HTTP/*)

#Output Setup
add_executable(test_httpclient main.cpp test_utils.cpp ${http_source_files})

#Link setup
target_link_libraries(test_httpclient ${GTEST_LIBRARIES} pthread curl)

SETUP_TARGET_FOR_COVERAGE(
           coverage_httpclient  # Name for custom target.
           test_httpclient      # Name of the test driver executable that runs the tests.
                                # NOTE! This should always have a ZERO as exit code
                                # otherwise the coverage generation will not complete.
           coverage             # Name of output directory.
           "/home/amzoughi/Test/http_github.ini"  # Optional fourth parameter is passed as arguments to _testrunner
                                                  # Pass them in list form, e.g.: "-j;2" for -j 2
           )
ELSE(CMAKE_BUILD_TYPE MATCHES Coverage)

link_directories(../lib/${CMAKE_BUILD_TYPE})

#Output Setup
add_executable(test_httpclient main.cpp test_utils.cpp)

#Link setup
target_link_libraries(test_httpclient httpclient ${GTEST_LIBRARIES} pthread curl)

ENDIF(CMAKE_BUILD_TYPE MATCHES Coverage)
