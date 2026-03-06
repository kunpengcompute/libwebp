# 部署指南

## 简介

本文档提供了鲲鹏图像库的安装指南、使用示例，旨在帮助用户快速熟悉鲲鹏图像库的使用。

KIL（Kunpeng Image Library，鲲鹏图像库）使用了向量优化，算法优化，流程优化等方法针对开源图像库libwebp的图片编码性能进行了优化。

- libwebp是WebP图像格式的官方库，它支持有损和无损压缩，并具备透明通道和动画功能。相比传统格式（如JPEG或PNG），WebP通常可以在保证视觉质量的前提下显著减小文件大小，是网页图像优化的理想选择。

## 已验证环境

为保证您可以顺利安全地使用鲲鹏图像库，请确保所使用的环境信息在已验证环境范围内。

**表 1**  已验证环境

|项目|说明|
|--|--|
|CPU型号|鲲鹏920系列处理器|
|OS|openEuler 22.03 LTS SP4|
|编译器|GCC 10.3.1|

## libwebp安装

### 安装

鲲鹏图像库基于开源社区libwebp进行性能优化，合入优化的patch编译安装后即可使用。

1. 安装基础环境。

    ```shell
    yum install libpng libpng-devel make zlib-devel cmake gcc gcc-c++ zip unzip which wget expect man pcre-devel autoconf automake libtool nasm bzip2-devel libtool-ltdl-devel gettext-devel texinfo git vim zip perl-IPC-Cmd gdb file -y
    ```

2. 进入libwebp源码目录，复制0001-webp_1.2.1-optimize-v1.0.1.patch到当前目录，并合入patch。

    ```shell
    cd /home/software/libwebp-1.2.1/
    git config --global --add safe.directory /home/software/libwebp-1.2.1
    git init && git add . && git commit -m "init"
    git apply 0001-webp_1.2.1-optimize-v1.0.1.patch
    ```

3. 切换到"/home/software"目录下，将以下代码保存到build.sh脚本中。

    ```shell
    #!/bin/bash
    # 定义安装位置
    INSTALL_PATH="/opt/webpInstall" 
    echo "$INSTALL_PATH/lib" > /etc/ld.so.conf.d/install.conf
    echo "$INSTALL_PATH/lib64" > /etc/ld.so.conf.d/install_lib64.conf
    export PKG_CONFIG_PATH="$INSTALL_PATH/lib/pkgconfig:$PKG_CONFIG_PATH"
    pushd libwebp-1.2.1/ && rm -rf build && mkdir build && pushd build && \
    cmake -DCMAKE_INSTALL_PREFIX:PATH=$INSTALL_PATH -DCMAKE_INSTALL_LIBDIR=lib -DCMAKE_INSTALL_FULL_LIBDIR=/lib -DBUILD_SHARED_LIBS=ON -DCMAKE_VERBOSE_MAKEFILE=OFF -DCMAKE_C_FLAGS="-ffp-contract=off" .. && \
    make -j8 && make install && popd && popd \
    ```

4. 执行脚本，等待脚本执行完成。

    ```shell
    sh build.sh
    ```

5. 验证是否安装成功。

    ```shell
    export LD_LIBRARY_PATH=/opt/webpInstall/lib/:/opt/webpInstall/lib64/:$LD_LIBRARY_PATH
    /opt/webpInstall/bin/webpmux -version
    ```

    如果回显正常显示WebP的版本号，说明安装成功。
