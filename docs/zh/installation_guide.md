# 鲲鹏LibWebP安装指南

## 安装说明

本文主要介绍鲲鹏优化版LibWebP的安装步骤。

## 环境要求

**硬件环境**

| 项目  | 说明    |
| ------------ | ------------ |
| CPU | 鲲鹏920新型号处理器 |

**操作系统要求**

| 项目  | 版本    |
| ------------ | ------------ |
| OS | openEuler 22.03 LTS SP4 |

**软件环境**

| 项目  | 版本    |
| ------------ | ------------ |
| 编译器 | GCC 10.3.1 或以上版本|
| make | 4.3 或以上版本|
| cmake | 3.10 或以上版本|

## 安装LibWebP

鲲鹏图像库基于开源社区LibWebP进行性能优化，合入优化的patch编译安装后即可使用。

1. 安装基础环境。

    ```shell
    yum install libpng libpng-devel make zlib-devel cmake gcc gcc-c++ zip unzip which wget expect man pcre-devel autoconf automake libtool nasm bzip2-devel libtool-ltdl-devel gettext-devel texinfo git vim zip perl-IPC-Cmd gdb file -y
    ```

2. 获取源码和补丁。

    ```shell
    cd /home/software/
    git clone https://gitcode.com/boostkit/libwebp.git
    tar -zxvf libwebp/libwebp-1.2.1.tar.gz -C ./
    cp libwebp/0001-webp_1.2.1-optimize-v1.0.1.patch libwebp-1.2.1/
    ```

3. 进入libwebp源码目录，并合入patch。

    ```shell
    cd /home/software/libwebp-1.2.1/
    git config --global --add safe.directory /home/software/libwebp-1.2.1
    git init && git add . && git commit -m "init"
    git apply 0001-webp_1.2.1-optimize-v1.0.1.patch
    ```

4. 切换到"/home/software"目录下，将以下代码保存到build.sh脚本中。

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

5. 执行脚本，等待脚本执行完成。

    ```shell
    sh build.sh
    ```

6. 验证是否安装成功。

    ```shell
    export LD_LIBRARY_PATH=/opt/webpInstall/lib/:/opt/webpInstall/lib64/:$LD_LIBRARY_PATH
    /opt/webpInstall/bin/webpmux -version
    ```

    如果回显正常显示WebP的版本号，说明安装成功。
