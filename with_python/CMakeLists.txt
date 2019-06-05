function(show_cmake_variables)
  message("Below is showing some cmake-variables ...")
  message("APPLE: ${APPLE}")
  message("XCODE: ${XCODE}")
  message("XCODE_VERSION: ${XCODE_VERSION}")
  message("WIN32: ${WIN32}")
  message("MSVC: ${MSVC}")
  message("MSVC_VERSION: ${MSVC_VERSION}")
  message("CMAKE_GENERATOR: ${CMAKE_GENERATOR}")
  message("CMAKE_SOURCE_DIR: ${CMAKE_SOURCE_DIR}")
  message("CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")
  message("CMAKE_BINARY_DIR: ${CMAKE_BINARY_DIR}")
  message("CMAKE_CURRENT_BINARY_DIR: ${CMAKE_CURRENT_BINARY_DIR}")
  message("CMAKE_COMMAND: ${CMAKE_COMMAND}")
  message("CMAKE_VERSION: ${CMAKE_VERSION}")
  message("CMAKE_SIZEOF_VOID_P: ${CMAKE_SIZEOF_VOID_P}")
  message("PROJECT_NAME: ${PROJECT_NAME}")
  message("CMAKE_HOST_SYSTEM: ${CMAKE_HOST_SYSTEM}")
  message("CMAKE_CXX_STANDARD: ${CMAKE_CXX_STANDARD}")
  message("PYTHON_EXECUTABLE: ${PYTHON_EXECUTABLE}")
  message("PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")
  message("Above is showing some cmake-variables ...")
  message("")
endfunction()

message("Welcome to use cmake")
show_cmake_variables()

cmake_minimum_required(VERSION 2.8.9)
project(hello)
add_executable(hello hello.c)

# must after project(hello) for getting PROJECT_SOURCE_DIR 
include(FindPythonInterp)
message("PYTHON_VERSION_STRING: ${PYTHON_VERSION_STRING}")
execute_process(
  COMMAND ${PYTHON_EXECUTABLE} "test.py" arg
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
  RESULT_VARIABLE PYTHON_RESULT
  OUTPUT_VARIABLE PYTHON_OUTPUT)
message("PYTHON_RESULT: ${PYTHON_RESULT}")
message("PYTHON_OUTPUT: ${PYTHON_OUTPUT}")

message("After running camke statements\n")
show_cmake_variables()
