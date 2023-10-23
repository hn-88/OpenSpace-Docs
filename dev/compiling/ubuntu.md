# Ubuntu

OpenSpace requires the latest LTS version (22.04) as the Qt6 dependency is not available for 20.04 and before. For details on older versions of Ubuntu, see the "Outdated Versions" section below.

  1. Install all of the necessary dependencies: `sudo apt install glew-utils libpng-dev freeglut3-dev git libxrandr-dev libxinerama-dev xorg-dev libxcursor-dev libcurl4-openssl-dev libxi-dev libasound2-dev libgdal-dev libboost1.74-dev qt6-base-dev libmpv-dev libvulkan-dev`
  1. If it not provided through `apt`, install at least version 3.25 of CMake by
     1. `wget https://github.com/Kitware/CMake/releases/download/v3.25.0/cmake-3.25.0-linux-x86_64.sh -q -O /tmp/cmake-install.sh`
     1. `chmod u+x /tmp/cmake-install.sh`
     1. `mkdir /opt/cmake`
     1. `/tmp/cmake-install.sh --skip-license --prefix=/opt/cmake`
     1. `ln -s /opt/cmake/bin/* /usr/local/bin`
  1. Clone the repository: `git clone --recursive https://github.com/OpenSpace/OpenSpace`
  1. `cd OpenSpace && mkdir build && cd build` followed by `cmake ..` and then `make -j`

## Developer Tools
Install the following tools:
 - Git 2.7+
 - GCC 11+
 - CMake 3.25+


There are specific CMake variables that need to be set. See the "Compile OpenSpace" section below for specific commandline arguments to CMake, including CMAKE_BUILD_TYPE, CMAKE_CXX_FLAGS, OpenGL_GL_PREFERENCE, etc. If using the CMake GUI, these will need to be set manually.

## Compile OpenSpace
1. Checkout
1. Configure
1. Build

```
openSpaceHome="$HOME/source/OpenSpace"

git clone --recursive https://github.com/OpenSpace/OpenSpace "$openSpaceHome"

mkdir -p "$openSpaceHome/build"
cd "$openSpaceHome/build" || exit

cmake \
-DCMAKE_BUILD_TYPE:STRING="Release" \
-DCMAKE_CXX_COMPILER:FILEPATH=/usr/bin/g++-11 \
-DCMAKE_C_COMPILER:FILEPATH=/usr/bin/gcc-11 \
-DASSIMP_BUILD_MINIZIP=1 "$openSpaceHome"

make
```

### CMake step
This can be one of the following:
* `cmake ..` The simplest way if there are no CMake issues to resolve or build options to set.
* `ccmake ..` A curses pseudo-gui version of CMake (run `sudo apt install cmake-curses-gui` if not installed).
* `cmake-gui ..` A Qt GUI version of CMake.

With any of these methods, CMake is first configured, then (if no errors) the generate step creates the build files.

The Web GUI interface does not work with the linux version of OpenSpace. Use the F1 menu instead, which provides full functionality but has a different layout.

## Run OpenSpace
Execute:

```
"$openSpaceHome"/bin/OpenSpace
```

## Outdated Versions of Ubuntu
Currently, pre-22.04 versions of Ubuntu use versions of `libmpv-dev` that are too old. An up-to-date debian package for mpv installation can be downloaded from [here](https://mpv.io/installation/), which would install an updated version of `libmpv-dev`.

Similarly, `qt6-base-dev` is not available but can be installed through other means such as [aqtinstall](https://github.com/miurahr/aqtinstall)

You can install gcc-11 using the following commands in case it is not supported:
The final commands configure Ubuntu's "update-alternatives", which allows a user to select among multiple installations of gcc:
```
sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade && sudo apt-get autoclean && sudo apt-get autoremove
```
reboot in case there are kernel changes

```
sudo apt-get install build-essential software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-11 g++-11
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 60 --slave /usr/bin/g++ g++ /usr/bin/g++-11
sudo update-alternatives --config gcc
```

If you don't want to install GCC 11 globally, you can overwrite the CMake options instead:
```
CMAKE_CXX_COMPILER:FILEPATH=/usr/bin/g++-11
CMAKE_C_COMPILER:FILEPATH=/usr/bin/gcc-11
```

If you do want to change the defaults: [here](https://stackoverflow.com/questions/7832892/how-to-change-the-default-gcc-compiler-in-ubuntu)




## Troubleshooting
Make sure that you are using the correct version of gcc/g++  
 - Double check `CMAKE_CXX_COMPILER` and `/usr/bin/c++ --version` to be sure.  It should be at least 8.0

Error: libstdc++.so.6: could not read symbols: Missing DSO from command line  
 - Try using g++ instead of gcc.

Error: GLSL 3.00 is not supported.  Supported versions are: 1.10, 1.20, 1.30, 1.00 ES, and 3.00 ES  
 - Enter the following line in the terminal before running, or add this to `~/.bashrc` or `~/.profile`:
 `export MESA_GL_VERSION_OVERRIDE=4.3`
 
Set a number format on your system that uses the dot as decimal separator. Otherwise you might see errors like this from OpenGL complaining about invalid numbers, e.g. `error: syntax error, unexpected INTCONSTANT, expecting IDENTIFIER or TYPE_IDENTIFIER or NEW_IDENTIFIER`.
 - Before launching, set the system locale to `en_US` or similar: `export LC_NUMERIC="en_US.UTF-8"` 
 
It may be necessary to install boost if trying to build some of the older branches.