build - run following shell commands to build the app:
mkdir build && cd build
cmake ..
make
./hello

clean - run rm to delete build folder:
rm -fr build

debug - if make a build shell script as build.sh, run following shell command to print detail log information for debugging:
bash -x build.sh
