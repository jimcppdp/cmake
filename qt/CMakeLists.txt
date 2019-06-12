#############################  function declaration  ##########################

function(set_project_config)

  set(APP_COMPANY "Abc" CACHE INTERNAL "app company")
  set(PROJ_NAME "helloworld" CACHE INTERNAL "project name") # CACHE INTERNAL means global variable definition in cmake

  set(CPACK_PACKAGE_VERSION_MAJOR "1" CACHE INTERNAL "")
  set(CPACK_PACKAGE_VERSION_MINOR "0" CACHE INTERNAL "")
  set(CPACK_PACKAGE_VERSION_PATCH "0" CACHE INTERNAL "")
  set(CPACK_PACKAGE_VERSION "${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}" CACHE INTERNAL "")

  set(CPACK_GENERATOR "ZIP" CACHE INTERNAL "")


  set(PROJ_VER ${CPACK_PACKAGE_VERSION} CACHE INTERNAL "project version")

  # note. environment variable QTDIR is required in Windows
  #       required in Macos if launch directly from finder, or make a dmg installer
  #
  if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    # placeholder
  elseif(WIN32)
    set(CMAKE_PREFIX_PATH $ENV{QTDIR} CACHE INTERNAL "qt path")
    message("Using Qt at path: " ${CMAKE_PREFIX_PATH})

  endif()

endfunction()


function(show_cmake_variables)
  message("Below is showing some cmake-variables ...")

  # cmake build in system variables
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
  message("MACOSX_BUNDLE_INFO_STRING: ${MACOSX_BUNDLE_INFO_STRING}")
  message("MACOSX_BUNDLE_GUI_IDENTIFIER: ${MACOSX_BUNDLE_GUI_IDENTIFIER}")
  message("MACOSX_BUNDLE_BUNDLE_LONG_VERSION_STRING: ${MACOSX_BUNDLE_BUNDLE_LONG_VERSION_STRING}")
  message("MACOSX_BUNDLE_BUNDLE_NAME: ${MACOSX_BUNDLE_BUNDLE_NAME}")
  message("MACOSX_BUNDLE_BUNDLE_SHORT_VERSION_STRING: ${MACOSX_BUNDLE_BUNDLE_SHORT_VERSION_STRING}")

  # self created variables
  message("CMAKE_PREFIX_PATH: ${CMAKE_PREFIX_PATH}")
  message("VC_INSTALLDIR: ${VC_INSTALLDIR}")
  message("CPACK_PACKAGE_VERSION: ${CPACK_PACKAGE_VERSION}")
  message("CPACK_PACKAGE_EXECUTABLES: ${CPACK_PACKAGE_EXECUTABLES}")


  message("Above is showing some cmake-variables ...")
  message("")
endfunction()

function(set_macos_bundle_variables)
  #set(MACOSX_BUNDLE_BUNDLE_VERSION ${PROJ_VER} PARENT_SCOPE) #set variable scope in parent, not local and not global
  set(MACOSX_BUNDLE_BUNDLE_VERSION ${PROJ_VER} CACHE INTERNAL "macos bundle version") # set variable scope in global
  set(PROJ_ICON "apple.icns" CACHE INTERNAL "macos icon name")

  set(MACOSX_BUNDLE_ICON_FILE ${PROJ_ICON} CACHE INTERNAL "macos bundle icon file") # set macos icon file

  set(MACOSX_BUNDLE_EXECUTABLE_NAME ${PROJECT_NAME} CACHE INTERNAL "")
  set(MACOSX_BUNDLE_COPYRIGHT "(c) 2019 Abc Inc." CACHE INTERNAL "")
endfunction()

#############################  pre cmake project  #############################

message("Welcome to use cmake")
set_project_config()
show_cmake_variables()

##############################  cmake project  ################################

cmake_minimum_required(VERSION 3.1.0)
project(helloworld)
# Find includes in corresponding build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)
# Instruct CMake to run moc automatically when needed
set(CMAKE_AUTOMOC ON)
# Create code from a list of Qt designer ui files
set(CMAKE_AUTOUIC ON)
# Find the QtWidgets library
find_package(Qt5Widgets CONFIG REQUIRED)
# Populate a CMake variable with the sources
set(project_SRCS
  mainwindow.ui
  mainwindow.cpp
  main.cpp
)

###########################  platform dependent ###############################

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")

  file(GLOB PLATFORM_SOURCES apple.icns)

  SET_SOURCE_FILES_PROPERTIES(
    ${PROJ_ICON}
    PROPERTIES
    MACOSX_PACKAGE_LOCATION Resources
  )

  set_macos_bundle_variables()

  # Tell CMake to create the executable
  add_executable(${PROJECT_NAME} MACOSX_BUNDLE ${project_SRCS} ${PLATFORM_SOURCES})
  # Use the Widgets module from Qt 5
  target_link_libraries(${PROJECT_NAME} Qt5::Widgets)

  # notetice. this is the key to fix the launch fail from finder in macos. the package folder including more files such as shared lib ...
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND $ENV{QTDIR}/clang_64/bin/macdeployqt ${PROJECT_NAME}.app -dmg)

  # just test cpack if it can make zip on macos, no need due to -dmg
  install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}.app DESTINATION .)
  include(CPack)

elseif(WIN32)

  set(CMAKE_PREFIX_PATH $ENV{QTDIR}) # set CMAKE_PREFIX_PATH is required in Windows
  message("Using Qt at path: " ${CMAKE_PREFIX_PATH})
  # Tell CMake to create the executable
  add_executable(${PROJECT_NAME} WIN32 ${project_SRCS} )
  # Use the Widgets module from Qt 5
  target_link_libraries(${PROJECT_NAME} Qt5::Widgets)

  # doing below COMMAND set "VCINSTALLDIR=${VC_INSTALLDIR}" 
  #   is just for removing warning for windeployqt
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND set "VCINSTALLDIR=${VC_INSTALLDIR}"
      COMMAND $ENV{QTDIR}/bin/windeployqt --dir "${CMAKE_CURRENT_BINARY_DIR}/deploy/packages" --no-compiler-runtime
      ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/${PROJECT_NAME}.exe)

  #set(CMAKE_INSTALL_UCRT_LIBRARIES TRUE)
  #include(InstallRequiredSystemLibraries)
  set(CPACK_PACKAGE_EXECUTABLES ${PROJECT_NAME} "Hello World")
  #set(CPACK_CREATE_DESKTOP_LINKS ON) # not working now
  #set(CPACK_PACKAGE_ICON "${CMAKE_CURRENT_SOURCE_DIR}/win.ico") # not working now
  set(CPACK_PACKAGE_VENDOR ${APP_COMPANY})

  install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/deploy/packages/ DESTINATION bin)
  install(TARGETS ${PROJECT_NAME} DESTINATION bin)
  include(CPack)

endif()

###########################  post cmake project  ##############################

message("\nAfter running camke statements\n")
show_cmake_variables()
