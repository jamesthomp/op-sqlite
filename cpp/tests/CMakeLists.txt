cmake_minimum_required(VERSION 3.22)
project(op-sqlite-tests)

set(CMAKE_CXX_STANDARD 20)
set(OP_SQLITE_SRC_DIR "${CMAKE_SOURCE_DIR}/..")
set(HERMES_SRC_DIR "${CMAKE_SOURCE_DIR}/hermes")
set(HERMES_BUILD_DIR "${CMAKE_SOURCE_DIR}/hermes-debug")


# Validate HERMES_SRC_DIR by checking for API/jsi/jsi/jsi.h
if (NOT EXISTS "${HERMES_SRC_DIR}/API/jsi/jsi/jsi.h")
    message(FATAL_ERROR "HERMES_SRC_DIR does not contain API/jsi/jsi/jsi.h")
endif ()

# Validate HERMES_BUILD_DIR by checking for bin/hermes with the platform-specific extension
if (NOT EXISTS "${HERMES_BUILD_DIR}/bin/hermes${CMAKE_EXECUTABLE_SUFFIX}")
    message(FATAL_ERROR "HERMES_BUILD_DIR does not contain bin/hermes${CMAKE_EXECUTABLE_SUFFIX}")
endif ()

include_directories("${HERMES_SRC_DIR}/API")
include_directories("${HERMES_SRC_DIR}/API/jsi")
include_directories("${HERMES_SRC_DIR}/public")

link_directories("${HERMES_BUILD_DIR}/API/hermes")
link_directories("${HERMES_BUILD_DIR}/jsi")

include(FetchContent)
FetchContent_Declare(
    Catch2
    GIT_REPOSITORY https://github.com/catchorg/Catch2.git
    GIT_TAG v3.6.0
)

FetchContent_Declare(
    folly
    GIT_REPOSITORY https://github.com/facebook/folly.git
    GIT_TAG v2024.07.29.00
)

FetchContent_MakeAvailable(Catch2)
FetchContent_MakeAvailable(folly)

file(GLOB TEST_FILES "${CMAKE_SOURCE_DIR}/*.cpp")
file(GLOB OP_SQLITE_FILES "${OP_SQLITE_SRC_DIR}/*.h" "${OP_SQLITE_SRC_DIR}/*.cpp")

include_directories(${OP_SQLITE_FILES})

add_executable(op-sqlite-tests ${TEST_FILES})
target_link_libraries(op-sqlite-tests hermes jsi folly Catch2::Catch2WithMain)

include(CTest)
include(Catch)
catch_discover_tests(op-sqlite-tests)