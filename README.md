# OpenXR-Hpp Basic Example

This project contains a simple [``OpenXR``](https://www.khronos.org/openxr/) example exploiting the ``OpenXR-Hpp`` language projection. It is a single file, ``CMake`` based, application that simply displays the following 
![image](https://user-images.githubusercontent.com/18591940/116436284-8bb7ee00-a84c-11eb-8e86-be0258b3560d.png).

It is based on the ``sdl2_gl_single_file_example.cpp`` example  from https://github.com/jherico/OpenXR-Samples.

## Dependencies
- [``CMake``](https://cmake.org/) (minimum version 3.10) 
- [``OpenXR``](https://github.com/KhronosGroup/OpenXR-SDK)
- [``OpenXR-Hpp``](https://github.com/KhronosGroup/OpenXR-Hpp) :warning: You need to run the ``generate-openxr-hpp`` script in order to generate the files. Then, set the ``OpenXR-Hpp_DIR`` environmental variable with the path to the generated files.
- [``OpenGL``](https://opengl.org/)
- [``SDL2``](https://www.libsdl.org/index.php)
- [``glfw3``](https://www.glfw.org/)

All these dependencies, except ``OpenXR-Hpp``, are available through ``apt`` and [``vcpkg``](https://github.com/microsoft/vcpkg).

## Installation
###  Linux/macOS
```sh
git clone https://github.com/S-Dafarra/OpenXR-Hpp-BasicExample
cd OpenXR-Hpp-BasicExample
mkdir build && cd build
cmake ../
make
```
### Windows
With IDE build tool facilities, such as Visual Studio:
```sh
git clone https://github.com/S-Dafarra/OpenXR-Hpp-BasicExample
cd OpenXR-Hpp-BasicExample
mkdir build && cd build
cmake ..
cmake --build . --target ALL_BUILD --config Release
```

### Testing
### Linux
This project can be compiled and tested even without a VR device on Windows through ``monado``.
Install ``monado`` with 
```
# Install monado packages with monado ppa ( https://monado.freedesktop.org/packages-ubuntu.html )
sudo add-apt-repository ppa:monado-xr/monado
sudo apt-get update
sudo apt install libopenxr1-monado
```
Then, in a terminal launch ``monado-service``. This will open a window with a grey background. If you are having issues in launching ``monado-service``, please check https://monado.freedesktop.org/getting-started.html#running-openxr-applications.

In a second terminal, assuming to be in the ``OpenXR-Hpp-BasicExample`` folder, simply run
```
./build/bin/OpenXR-Hpp-BasicExample
```

### Windows
TBD. At the moment, this has been tested only on Ubuntu 20.04.
