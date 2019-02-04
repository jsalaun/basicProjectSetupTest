cmake_minimum_required(VERSION 3.2)

if (MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

add_executable(intro.exe main.cpp)
target_compile_features(intro.exe PRIVATE cxx_lambda_init_captures)


add_executable(tester.exe tester.cpp)
add_test(Test tester.exe)