cmake_minimum_required(VERSION 3.16)
project(cpp_quality_testbed CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(SEED_DEFECTS "Also build the intentionally defective code in seeded/" OFF)
option(ENABLE_COVERAGE "Build with gcov instrumentation" OFF)
option(ENABLE_SANITIZERS "Build with AddressSanitizer + UBSan" OFF)

add_compile_options(-Wall -Wextra -Wpedantic)
if(ENABLE_COVERAGE)
  add_compile_options(--coverage -O0)
  add_link_options(--coverage)
endif()
if(ENABLE_SANITIZERS)
  add_compile_options(-fsanitize=address,undefined -fno-sanitize-recover=undefined -fno-omit-frame-pointer -O1)
  add_link_options(-fsanitize=address,undefined)
endif()

add_library(core src/calculator.cpp src/strings.cpp)
target_include_directories(core PUBLIC src)

enable_testing()
add_executable(test_core tests/test_core.cpp)
target_link_libraries(test_core core)
add_test(NAME core COMMAND test_core)

if(SEED_DEFECTS)
  add_library(seeded
    seeded/memory_bugs.cpp seeded/null_deref.cpp seeded/complex.cpp
    seeded/uncovered.cpp seeded/badformat.cpp seeded/duplicate_a.cpp
    seeded/duplicate_b.cpp seeded/warnings.cpp seeded/overflow.cpp)
  target_include_directories(seeded PUBLIC seeded)
  add_executable(test_seeded tests/test_seeded.cpp)
  target_link_libraries(test_seeded seeded)
  add_test(NAME seeded_safe COMMAND test_seeded safe)
endif()
