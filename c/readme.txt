Windows:

mkdir build && cd build
cmake ..
cmake --build .
Debug/hello.exe

clean - run rmdir to delete build folder:
rmdir /s/q build

==========================================================================

MacOS:

build - run following shell commands to build the app:
mkdir build && cd build
cmake ..
cmake --build . 
or make
./hello

clean - run rm to delete build folder:
rm -fr build

debug - if make a build shell script as build.sh, run following shell command to print detail log information for debugging:
bash -x build.sh
