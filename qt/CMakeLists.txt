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
  message("Above is showing some cmake-variables ...")
  message("")
endfunction()

message("Welcome to use cmake")
show_cmake_variables()

cmake_minimum_required(VERSION 3.1.0)
project(qt_project)
# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
# Instruct CMake to run moc automatically when needed
set(CMAKE_AUTOMOC ON)
# Create code from a list of Qt designer ui files
set(CMAKE_AUTOUIC ON)
# Find the QtWidgets library
find_package(Qt5Widgets CONFIG REQUIRED)
# Populate a CMake variable with the sources
set(helloworld_SRCS
	mainwindow.ui
	mainwindow.cpp
	main.cpp
)
# Tell CMake to create the helloworld executable
add_executable(helloworld WIN32 ${helloworld_SRCS})
# Use the Widgets module from Qt 5
target_link_libraries(helloworld Qt5::Widgets)

message("After running camke statements\n")
show_cmake_variables()
