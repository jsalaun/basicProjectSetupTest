cmake_minimum_required(VERSION 3.0)
#set(CMAKE_CXX_STANDARD 14)
#set(CC clang-cl)

if (MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

add_executable(intro.exe main.cpp)
target_compile_features(intro.exe PRIVATE cxx_lambda_init_captures)

enable_testing()
add_executable(tester.exe tester.cpp)
add_test(Test tester.exe)