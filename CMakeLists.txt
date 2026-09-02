cmake_minimum_required(VERSION 3.16)
project(mini_rdbms CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug)
endif()

# Warnings-as-you-go, not warnings-as-errors yet (turn on -Werror once Phase 1+ stabilizes)
add_compile_options(-Wall -Wextra)
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(-g -O0 -fsanitize=address,undefined)
    add_link_options(-fsanitize=address,undefined)
endif()

include_directories(${CMAKE_SOURCE_DIR}/src)

# --- Core library: everything under src/ except main.cpp -------------------
file(GLOB_RECURSE RDBMS_SOURCES CONFIGURE_DEPENDS "src/*.cpp")
list(FILTER RDBMS_SOURCES EXCLUDE REGEX ".*/main\\.cpp$")

if(RDBMS_SOURCES)
    add_library(rdbms_core ${RDBMS_SOURCES})
    target_include_directories(rdbms_core PUBLIC ${CMAKE_SOURCE_DIR}/src)
else()
    add_library(rdbms_core INTERFACE)
    target_include_directories(rdbms_core INTERFACE ${CMAKE_SOURCE_DIR}/src)
endif()

# --- Main executable (REPL entry point) -------------------------------------
add_executable(mini_rdbms src/main.cpp)
target_link_libraries(mini_rdbms PRIVATE rdbms_core)

# --- GoogleTest (fetched, not vendored by hand) -----------------------------
include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG v1.15.2
)
# Keep GoogleTest's own settings from leaking into our build
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)

enable_testing()

# --- Test executable ---------------------------------------------------------
file(GLOB_RECURSE RDBMS_TEST_SOURCES CONFIGURE_DEPENDS "test/*.cpp")

if(RDBMS_TEST_SOURCES)
    add_executable(rdbms_tests ${RDBMS_TEST_SOURCES})
    target_link_libraries(rdbms_tests PRIVATE rdbms_core gtest gtest_main)
    target_include_directories(rdbms_tests PRIVATE ${CMAKE_SOURCE_DIR}/src)

    include(GoogleTest)
    gtest_discover_tests(rdbms_tests)
endif()

# --- Benchmark executable (added once Phase 10 has something to measure) ----
if(EXISTS "${CMAKE_SOURCE_DIR}/benchmark/bench_main.cpp")
    add_executable(rdbms_bench benchmark/bench_main.cpp)
    target_link_libraries(rdbms_bench PRIVATE rdbms_core)
endif()
