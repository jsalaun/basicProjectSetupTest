project(basicProject)
cmake_minimum_required(VERSION 3.1.2)
#set(CMAKE_CXX_STANDARD 14)
#set(CC clang-cl)
add_definitions("-std=c++11")

if (MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# Download automatically, you can also just copy the conan.cmake file
if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
   message(STATUS "Downloading conan.cmake from https://github.com/conan-io/cmake-conan")
   file(DOWNLOAD "https://raw.githubusercontent.com/conan-io/cmake-conan/v0.13/conan.cmake"
                 "${CMAKE_BINARY_DIR}/conan.cmake")
endif()

find_program(CONAN_CMD conan)
if(NOT CONAN_CMD)
    message(FATAL_ERROR "Conan executable not found!")
endif()
execute_process(COMMAND ${CONAN_CMD} --version
    OUTPUT_VARIABLE CONAN_VERSION_OUTPUT)
string(REGEX MATCH ".*Conan version ([0-9]+\.[0-9]+\.[0-9]+)" FOO "${CONAN_VERSION_OUTPUT}")
set(CONAN_VERSION_REQUIRED 1.0.0)
if(${CMAKE_MATCH_1} VERSION_LESS ${CONAN_VERSION_REQUIRED})
    message(FATAL_ERROR "Conan outdated. Installed: ${CONAN_VERSION}, \
        required: ${CONAN_VERSION_REQUIRED}. Consider updating via 'pip \
        install conan --upgrade'.")
endif()

execute_process(COMMAND ${CONAN_CMD} remote list OUTPUT_VARIABLE CONAN_REMOTES)

if("${CONAN_REMOTES}" MATCHES ".*conan-community:.*")
    execute_process(COMMAND ${CONAN_CMD} remote update -i 1 conan-community
        https://api.bintray.com/conan/conan-community/conan)
else()
    message(STATUS "Conan adding remote repositories \
        (https://api.bintray.com/conan/conan-community/conan)")
    execute_process(COMMAND ${CONAN_CMD} remote add -i 1 conan-community
        https://api.bintray.com/conan/conan-community/conan)
endif()

if("${CONAN_REMOTES}" MATCHES ".*catchorg:.*")
    execute_process(COMMAND ${CONAN_CMD} remote update -i 2 catchorg
        https://api.bintray.com/catchorg)
else()
    message(STATUS "Conan adding remote catchorg repository \
        (https://api.bintray.com/catchorg)")
    execute_process(COMMAND ${CONAN_CMD} remote add -i 2 catchorg
        https://api.bintray.com/catchorg)
endif()
message(STATUS "Got almost at the end")
include(${CMAKE_BINARY_DIR}/conan.cmake)
conan_cmake_run(CONANFILE conanfile.txt
                BASIC_SETUP CMAKE_TARGETS
                BUILD missing)

message(STATUS "Got almost at..")


#add_executable(CppLinuxVS Main.cpp)
#target_link_libraries(CppLinuxVS ${CONAN_LIST})

add_executable(intro.exe main.cpp)
message(STATUS "Got almost at the end2")
target_compile_features(intro.exe PRIVATE cxx_lambda_init_captures)
message(STATUS "Got almost at the end3")
target_link_libraries(intro.exe ${CONAN_LIST})
message(STATUS "Got almost at the end4")

enable_testing()
add_executable(tester.exe tester.cpp)
add_test(Test tester.exe)