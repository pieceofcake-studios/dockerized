<!--
  Copyright (c) 2018-2020 Yao Wei Tjong. All rights reserved.

  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files (the "Software"), to deal
  in the Software without restriction, including without limitation the rights
  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
  copies of the Software, and to permit persons to whom the Software is
  furnished to do so, subject to the following conditions:

  The above copyright notice and this permission notice shall be included in
  all copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
  THE SOFTWARE.
-->

# Native dockerized build environment for Urho3D

This branch of the repo contains the information to build and run the native DBE
for Urho3D.

Although the main purpose of this docker image is to build Urho3D project, any
Urho3D downstream projects that reuse Urho3D build system may benefit from the
dockerized build environment as well. Thus, you can just simply substitute all the
references to Urho3D project in the README.md with your own project.

## Building

You do not need to build this docker image yourself unless you want to change the
default locale by building it from a custom base image using alternative locale. 

```
$ docker build --tag=urho3d/dockerized-native .
```

## Running

There are two compiler toolchains to choose from: GCC (default) and Clang. Both
compiler toolchains are 64-bit (default) and 32-bit capable. To run with Clang
compiler toolchain, set the following environment variable: `CC=clang CXX=clang++`.
To run with 32-bit compiler toolchain variant, set `URHO3D_64BIT=0`. For example,
to choose 32-bit Clang compiler toolchain then run the native DBE as below.

```
$ docker run -it --rm \
    --mount type=bind,source=/host-path/to/urho3D,target=/local-path/to/urho3d \
    -e PROJECT_DIR=/local-path/to/urho3d \
    -e HOST_UID=1234 -e HOST_GID=1234 \
    -e CC=clang -e CXX=clang++ \
    -e URHO3D_64BIT=0 \
    urho3d/dockerized-native
```

The container's entry point eventually calls the default command `rake cmake &&
rake make`, which is equivalent to running `script/cmake_generic.sh build/native
&& cd build/native && make` command. When the container finishes running, all the
build artifacts can be found in the build tree. Other build options supported by
Urho3D build system can be passed when running the container too by using "-e"
flag. For example to build a shared library type, one can run the container as
below.

```
$ docker run -it --rm \
    --mount type=bind,source=/host-path/to/urho3D,target=/local-path/to/urho3d \
    -e PROJECT_DIR=/local-path/to/urho3d \
    -e HOST_UID=1234 -e HOST_GID=1234 \
    -e CC=clang -e CXX=clang++ \
    -e URHO3D_64BIT=0 \
    -e URHO3D_LIB_TYPE=SHARED \
    urho3d/dockerized-native
```

## Running using convenient shell script in Urho3D project

The last example can be easily achieved using the convenient shell script in
Urho3D project.

```
$ CC=clang CXX=clang++ URHO3D_64BIT=0 URHO3D_LIB_TYPE=SHARED script/dockerized.sh native
```
