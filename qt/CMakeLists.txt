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
  message("CMAKE_SYSTEM_NAME: ${CMAKE_SYSTEM_NAME}")
  message("CMAKE_SYSTEM_VERSION: ${CMAKE_SYSTEM_VERSION}")
  message("MACOSX_BUNDLE_BUNDLE_VERSION: ${MACOSX_BUNDLE_BUNDLE_VERSION}")
  message("MACOSX_BUNDLE_ICON_FILE: ${MACOSX_BUNDLE_ICON_FILE}")

  message("Above is showing some cmake-variables ...")
  message("")
endfunction()

function(set_macos_bundle_variables)
  #set(MACOSX_BUNDLE_BUNDLE_VERSION "1.23" PARENT_SCOPE) #set variable scope in parent, not local and not global
  set(MACOSX_BUNDLE_BUNDLE_VERSION "1.23" CACHE INTERNAL "macos bundle version") # set variable scope in global
  set(MACOSX_BUNDLE_ICON_FILE "apple.icns" CACHE INTERNAL "macos bundle icon file") # set macos icon file

  set(MACOSX_BUNDLE_EXECUTABLE_NAME "helloworld" CACHE INTERNAL "")
  set(MACOSX_BUNDLE_COPYRIGHT "(c) 2019 Abc Inc." CACHE INTERNAL "")  
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


if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")

  file(GLOB PLATFORM_SOURCES apple.icns)

  SET_SOURCE_FILES_PROPERTIES(
    apple.icns
    PROPERTIES
    MACOSX_PACKAGE_LOCATION Resources
  )

  set_macos_bundle_variables()

  # Tell CMake to create the helloworld executable
  add_executable(helloworld MACOSX_BUNDLE ${helloworld_SRCS} ${PLATFORM_SOURCES})
  # Use the Widgets module from Qt 5
  target_link_libraries(helloworld Qt5::Widgets)

elseif(WIN32)

  # Tell CMake to create the helloworld executable
  add_executable(helloworld WIN32 ${helloworld_SRCS} )
  # Use the Widgets module from Qt 5
  target_link_libraries(helloworld Qt5::Widgets)

endif()


message("\nAfter running camke statements\n")
show_cmake_variables()
