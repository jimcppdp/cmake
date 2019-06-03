###############################################################################
TODO:
  - setup/build/test/deploy
  - deploy a single installer on Windows

###############################################################################

Windows version of setup/build/deploy

  install qt for win
  use qtcreator to new a qt widget app to get three files (mainwindow.ui , mainwindow.cpp, main.cpp)
  mkdir build && cd build
  Create CMakeLists.txt

  below is the key difference with mac version in CMakeLists.txt

  set(CMAKE_PREFIX_PATH $ENV{QTDIR}) # set CMAKE_PREFIX_PATH is required in Windwos
  message("Using Qt at path: " ${CMAKE_PREFIX_PATH})

  # doing below COMMAND set "VCINSTALLDIR=${VC_INSTALLDIR}" 
  #   is just for removing warning for windeployqt
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND set "VCINSTALLDIR=${VC_INSTALLDIR}"
      COMMAND $ENV{QTDIR}/bin/windeployqt --dir "${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>" --no-compiler-runtime
      ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/${PROJECT_NAME}.exe)

  build method 1
    run 'cmake -G "Visual Studio 14 2015"' .. for build x86 32bit version
    run 'cmake --build .' to get runable debug version in build\Debug directory

  build method 2
    run 'cmake -G "Visual Studio 14 2015"' ..
    run 'cmake --build . --config Release' to get runable release version in build\Release directory

  build method 3
    run 'cmake -G "Visual Studio 14 2015"' ..
    run 'msbuild helloworld.vcxproj' to get runable debug version in build\Debug directory
    actually it's the same result as method 1

  build method 4
    run 'cmake -G "Visual Studio 14 2015"' ..
    run 'msbuild helloworld.vcxproj /property:Configuration=Release' to get runable release version in build\Release directory
    actually it's the same result as method 2

  build method 5
    run 'cmake -G "Visual Studio 14 2015"' ..
    run 'msbuild helloworld.sln /t:helloworld' to get runable debug version in build\Debug directory
    actually it's the same result as method 1

  build method 6
    run 'cmake -G "Visual Studio 14 2015"' ..
    run 'msbuild helloworld.sln /t:helloworld /property:Configuration=Release' to get runable release version in build\Release directory
    actually it's the same result as method 2

  some useful shell commands:
    rmdir /s /q build  - delete build directory including sub-directory

###############################################################################

macos version of setup/build/deploy
  similar to macos version of setup/build but add following

  # notetice. this is the key to fix the launch fail from finder in macos. the package folder including more files such as shared lib ...
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND $ENV{QTDIR}/bin/macdeployqt ${PROJECT_NAME}.app -dmg)

  build method 1
    run 'cmake ..' to generate  makefile
    run 'make' to get helloworld executable file with macos bundle app

  build method 2.1 2.2 3
    TODO: check if the same as below

###############################################################################

macos version of setup/build

  install qt for mac
  use qtcreator to new a qt widget app to get three files (mainwindow.ui , mainwindow.cpp, main.cpp)
  mkdir build && cd build
  Create CMakeLists.txt with content below


  cmake_minimum_required(VERSION 3.1.0)
  project(testproject)
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


  build method 1
    run 'cmake ..' to generate  makefile
    run 'make' to get helloworld executable file

  build method 2.1
    run 'cmake -G "Xcode"' .. to generate  xcode project file 
    run 'xcodebuild' to get  executable in  Debug folder

  build method 2.2
    run 'xcodebuild -configuration Release' to build release version

  build method 3
    using xcode to open the project and build / debug

  some useful shell commands:
    rm -fr build  - delete build folder including sub-folder   

