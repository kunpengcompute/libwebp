# Installation Guide

## Overview

This document provides the installation guide and usage examples of Kunpeng Image Library (KIL).

KIL uses methods such as vector optimization, algorithm optimization, and workflow optimization to improve the image encoding performance of LibWebP.

LibWebP is the official library of the WebP image format. It supports lossy and lossless compression, and features transparency (alpha channel) and animation. Compared with traditional formats (such as JPEG and PNG), WebP can significantly reduce the file size while ensuring image quality. It is an ideal choice for web page image optimization.

## Verified Environment

To use KIL smoothly and securely, ensure that your environment is one of the verified environments.

**Table 1** Verified environment

|Project|Description|
|--|--|
|CPU model|Kunpeng 920|
|OS|openEuler 22.03 LTS SP4|
|Compiler|GCC 10.3.1|

## Installing LibWebP

KIL optimizes the image encoding performance of LibWebP. You can use this library after applying optimization patches and compiling and installing the library.

1. Prepare the basic environment.

    ```shell
    yum install libpng libpng-devel make zlib-devel cmake gcc gcc-c++ zip unzip which wget expect man pcre-devel autoconf automake libtool nasm bzip2-devel libtool-ltdl-devel gettext-devel texinfo git vim zip perl-IPC-Cmd gdb file -y
    ```

2. Go to the LibWebP source code directory, copy `0001-webp_1.2.1-optimize-v1.0.1.patch` to the current directory, and apply the patch.

    ```shell
    cd /home/software/libwebp-1.2.1/
    git config --global --add safe.directory /home/software/libwebp-1.2.1
    git init && git add . && git commit -m "init"
    git apply 0001-webp_1.2.1-optimize-v1.0.1.patch
    ```

3. Switch to the `/home/software` directory and save the following code to the `build.sh` script:

    ```shell
    #!/bin/bash
    # Define the installation location.
    INSTALL_PATH="/opt/webpInstall" 
    echo "$INSTALL_PATH/lib" > /etc/ld.so.conf.d/install.conf
    echo "$INSTALL_PATH/lib64" > /etc/ld.so.conf.d/install_lib64.conf
    export PKG_CONFIG_PATH="$INSTALL_PATH/lib/pkgconfig:$PKG_CONFIG_PATH"
    pushd libwebp-1.2.1/ && rm -rf build && mkdir build && pushd build && \
    cmake -DCMAKE_INSTALL_PREFIX:PATH=$INSTALL_PATH -DCMAKE_INSTALL_LIBDIR=lib -DCMAKE_INSTALL_FULL_LIBDIR=/lib -DBUILD_SHARED_LIBS=ON -DCMAKE_VERBOSE_MAKEFILE=OFF -DCMAKE_C_FLAGS="-ffp-contract=off" .. && \
    make -j8 && make install && popd && popd \
    ```

4. Execute the script and wait until the execution is complete.

    ```shell
    sh build.sh
    ```

5. Check whether the installation is successful.

    ```shell
    export LD_LIBRARY_PATH=/opt/webpInstall/lib/:/opt/webpInstall/lib64/:$LD_LIBRARY_PATH
    /opt/webpInstall/bin/webpmux -version
    ```

    If the WebP version number is displayed in the command output, the installation is successful.
