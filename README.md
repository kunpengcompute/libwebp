# 项目介绍<a name="ZH-CN_TOPIC_0000002475598805"></a>

鲲鹏图像库libwebp代码仓使用了向量优化，算法优化，流程优化等方法针对开源图像库libwebp图片编码性能进行了优化。

-   libwebp是WebP图像格式的官方库，它支持有损和无损压缩，并具备透明通道和动画功能。相比传统格式（如JPEG或PNG），WebP通常可以在保证视觉质量的前提下显著减小文件大小，是网页图像优化的理想选择。

# 版本说明<a name="ZH-CN_TOPIC_0000002475678601"></a>

<a name="table740710612324"></a>
<table><thead align="left"><tr id="row144075603214"><th class="cellrowborder" valign="top" width="33.33333333333333%" id="mcps1.1.4.1.1"><p id="p114078693216"><a name="p114078693216"></a><a name="p114078693216"></a>操作系统</p>
</th>
<th class="cellrowborder" valign="top" width="33.33333333333333%" id="mcps1.1.4.1.2"><p id="p84081465329"><a name="p84081465329"></a><a name="p84081465329"></a>开源软件</p>
</th>
<th class="cellrowborder" valign="top" width="33.33333333333333%" id="mcps1.1.4.1.3"><p id="p19408196163219"><a name="p19408196163219"></a><a name="p19408196163219"></a>特性</p>
</th>
</tr>
</thead>
<tbody><tr id="row64089653211"><td class="cellrowborder" valign="top" width="33.33333333333333%" headers="mcps1.1.4.1.1 "><p id="p24084615327"><a name="p24084615327"></a><a name="p24084615327"></a><span>openEuler 22.03 LTS SP4</span></p>
</td>
<td class="cellrowborder" valign="top" width="33.33333333333333%" headers="mcps1.1.4.1.2 "><p id="p1040846123216"><a name="p1040846123216"></a><a name="p1040846123216"></a>libwebp-1.2.1</p>
</td>
<td class="cellrowborder" valign="top" width="33.33333333333333%" headers="mcps1.1.4.1.3 "><p id="p18561121519347"><a name="p18561121519347"></a><a name="p18561121519347"></a>图片编码性能优化</p>
</td>
</tr>
</tbody>
</table>

# 环境部署<a name="ZH-CN_TOPIC_0000002442158816"></a>

鲲鹏图像库基于开源社区libwebp进行性能优化，合入优化的patch编译安装后即可使用。

1.  安装基础环境。

    ```
    yum install make zlib-devel cmake gcc gcc-c++ zip unzip which wget expect man pcre-devel autoconf automake libtool nasm bzip2-devel libtool-ltdl-devel gettext-devel texinfo git vim zip perl-IPC-Cmd gdb file -y
    ```

2.  进入libwebp源码目录，复制huawei\_libwebp-1.2.1\_opt.patch到当前目录，并合入patch。

    ```
    cd /home/software/libwebp-1.2.1/
    git config --global --add safe.directory /home/software/libwebp-1.2.1
    git init && git add . && git commit -m "init"
    git apply huawei_libwebp-1.2.1_opt.patch
    ```

3.  切换到“/home/software“目录下，将以下代码保存到build.sh脚本中。

    ```
    #!/bin/bash
    JDIMG_PATH="/opt/jdimg" 
    echo "$JDIMG_PATH/lib" > /etc/ld.so.conf.d/jdimg.conf
    echo "$JDIMG_PATH/lib64" > /etc/ld.so.conf.d/jdimg_lib64.conf
    export PKG_CONFIG_PATH="$JDIMG_PATH/lib/pkgconfig:$PKG_CONFIG_PATH"
    pushd libwebp-1.2.1/ && rm -rf build && mkdir build && pushd build && \
    cmake -DCMAKE_INSTALL_PREFIX:PATH=$JDIMG_PATH -DCMAKE_INSTALL_LIBDIR=lib -DCMAKE_INSTALL_FULL_LIBDIR=/lib -DBUILD_SHARED_LIBS=ON -DCMAKE_VERBOSE_MAKEFILE=OFF -DCMAKE_C_FLAGS="-ffp-contract=off" .. && \
    make -j8 && make install && popd && popd && \
    pushd libjpeg-turbo-1.5.2 && chmod +x ./configure && rm -rf build && mkdir build && pushd build && \
    ../configure --prefix=$JDIMG_PATH CFLAGS="-O3 -ffp-contract=off" CPPFLAGS="-Wall -ffp-contract=off" && \
    make -j8 && make install && popd && popd && \
    pushd giflib-5.1.4/ && chmod +x ./configure && rm -rf build && mkdir build && pushd build && \
    ../configure --prefix=$JDIMG_PATH CPPFLAGS="-Wall -ffp-contract=off" && \
    make -j8 && make install && popd && popd && \
    pushd zlib-1.2.11 && chmod +x ./configure && rm -rf build && mkdir build && pushd build && \
    ../configure --prefix=$JDIMG_PATH && make -j8 && make install && popd && popd && \
    pushd libpng-1.6.38 && chmod +x ./configure && rm -rf build && mkdir build && pushd build && \
    ../configure --prefix=$JDIMG_PATH LDFLAGS="-L$JDIMG_PATH/lib/ -lz" CFLAGS="-g -O2 -ffp-contract=off -I$JDIMG_PATH/include" CPPFLAGS="-ffp-contract=off" && \
    make -j8 && make install && popd && popd && \
    pushd GraphicsMagick-1.3.38 && chmod +x ./configure && rm -rf build && mkdir build && pushd build && \
    ../configure --prefix=$JDIMG_PATH --disable-openmp --enable-shared --without-tiff --without-x LDFLAGS="-L$JDIMG_PATH/lib" CPPFLAGS="-I$JDIMG_PATH/include -ffp-contract=off " CFLAGS="-g -Ofast -Wall -ffp-contract=off " && make -j8 && make install && popd && popd && \
    ```

4.  执行脚本，等待脚本执行完成。

    ```
    sh build.sh
    ```

5.  验证是否安装成功。

    ```
    export LD_LIBRARY_PATH=/opt/jdimg/lib/:/opt/jdimg/lib64/:$LD_LIBRARY_PATH
    /opt/jdimg/bin/webpmux -version
    ```

    如果回显正常显示WebP的版本号，说明安装成功。

# 快速上手<a name="ZH-CN_TOPIC_0000002475598809"></a>

以下是将JPG格式图片解码再转换成WebP格式的使用示例。

1.  创建main.cpp文件，写入以下示例代码。

    ```
    #include <iostream>
    #include <fstream>
    #include <vector>
    #include <turbojpeg.h>
    #include <png.h>
    #include <gif_lib.h>
    #include <vector>
    #include <map>
    #include <algorithm>
    #include <memory>
    #include <stdexcept>
    #include <wand/magick_wand.h>
    #include <webp/encode.h>
    // JPG文件解码
    static bool decodeJPGtoRGBA(const std::string& inputPath, std::vector<uint8_t>& rgbaData, int& width, int& height) {
        // 打开JPEG文件
        std::ifstream inputFile(inputPath, std::ios::binary | std::ios::ate);
        if (!inputFile) {
            std::cerr << "无法打开文件: " << inputPath << std::endl;
            return false;
        }
        // 获取文件大小并读取数据
        std::streamsize fileSize = inputFile.tellg();
        inputFile.seekg(0, std::ios::beg);
        std::vector<unsigned char> jpegData(fileSize);
        if (!inputFile.read(reinterpret_cast<char*>(jpegData.data()), fileSize)) {
            std::cerr << "读取文件失败: " << inputPath << std::endl;
            return false;
        }
        // 创建TurboJPEG解码器实例
        tjhandle handle = tjInitDecompress();
        if (!handle) {
            std::cerr << "无法初始化TurboJPEG解码器" << std::endl;
            return false;
        }
        // 获取JPEG图片的宽高和像素格式
        int jpegSubsamp;
        if (tjDecompressHeader2(handle, jpegData.data(), jpegData.size(), &width, &height, &jpegSubsamp) != 0) {
            std::cerr << "无法解析JPEG头: " << tjGetErrorStr() << std::endl;
            tjDestroy(handle);
            return false;
        }
        // 分配RGBA数据存储空间
        rgbaData.resize(width * height * 4); // 每个像素4字节 (RGBA)
        // 解码JPEG数据为RGBA格式
        if (tjDecompress2(handle, jpegData.data(), jpegData.size(), rgbaData.data(), width, 0, height, TJPF_RGBA, TJFLAG_FASTDCT) != 0) {
            std::cerr << "JPEG解码失败: " << tjGetErrorStr() << std::endl;
            tjDestroy(handle);
            return false;
        }
        tjDestroy(handle);
        std::cout << "JPEG 文件已成功解码为RGBA格式" << std::endl;
        return true;
    }
    
    // 将RGBA数据编码为WebP文件
    bool encodeRGBAtoWebP(const std::string& outputPath, const uint8_t* rgbaData, int width, int height) {
        if (!rgbaData || width <= 0 || height <= 0) {
            std::cerr << "无效的输入参数" << std::endl;
            return false;
        }
        // 设置编码参数
        WebPConfig config;
        if (!WebPConfigInit(&config)) {
            std::cerr << "无法初始化 WebPConfig" << std::endl;
            return false;
        }
        config.lossless = 0;      // 使用有损压缩
        config.quality = 75.0f;   // 设定压缩质量
        config.method = 6;        // 编码速度（越大越慢但压缩率更高）
        // RGBA转WebP图片结构
        WebPPicture picture;
        if (!WebPPictureInit(&picture)) {
            std::cerr << "无法初始化 WebPPicture" << std::endl;
            return false;
        }
        picture.width = width;
        picture.height = height;
        picture.use_argb = 0; // 使用YUV模式编码
        // 将RGBA数据导入到WebPPicture
        if (!WebPPictureImportRGBA(&picture, rgbaData, width * 4)) {
            std::cerr << "无法导入RGBA数据" << std::endl;
            WebPPictureFree(&picture);
            return false;
        }
        // 输出内存结构体
        WebPMemoryWriter writer;
        WebPMemoryWriterInit(&writer);
        picture.writer = WebPMemoryWrite;
        picture.custom_ptr = &writer;
        // 进行编码
        if (!WebPEncode(&config, &picture)) {
            std::cerr << "WebP编码失败，错误代码：" << picture.error_code << std::endl;
            WebPPictureFree(&picture);
            WebPMemoryWriterClear(&writer);
            return false;
        }
        // 写入文件
        std::ofstream outFile(outputPath, std::ios::binary);
        if (!outFile.write(reinterpret_cast<char*>(writer.mem), writer.size)) {
            std::cerr << "无法写入文件" << std::endl;
            WebPPictureFree(&picture);
            WebPMemoryWriterClear(&writer);
            return false;
        }
        WebPPictureFree(&picture);
        WebPMemoryWriterClear(&writer);
        std::cout << "WebP文件已成功生成: " << outputPath << std::endl;
        return true;
    }
    int main()
    {
        std::vector<uint8_t> rgbaData;
        int width, height;
        std::string outputPathWebp = "./1.webp";
        std::string inputPath = "/home/imgtest/1.jpg";
        bool decode = decodeJPGtoRGBA(inputPath, rgbaData, width, height);
        bool encodeWebp = encodeRGBAtoWebP(outputPathWebp, rgbaData.data(), width, height); // 转换成WebP格式
        return 0;
    }
    ```

2.  编译执行。

    ```
    g++ -std=c++17 -I/opt/jdimg/include -I/opt/jdimg/include/GraphicsMagick -I/opt/jdimg/include/webp     -L/opt/jdimg/lib -L/opt/jdimg/lib64     -lturbojpeg -lpng -lgif -lwebp     -lGraphicsMagick -lGraphicsMagick++ -lGraphicsMagickWand     -Wl,-rpath,/opt/jdimg/lib64  main.cpp -o main && ./main
    ```

# 贡献指南<a name="ZH-CN_TOPIC_0000002475598813"></a>

如果使用过程中有任何问题，或者需要反馈特性需求和bug报告，可以提交issue联系我们，具体贡献方法可参考[这里](https://gitcode.com/boostkit/community/blob/master/docs/contributor/contributing.md)。

# 免责声明<a name="ZH-CN_TOPIC_0000002475678597"></a>

此代码仓基于libwebp开源代码提供patch，仅作libwebp图片编码性能提升，编码风格遵照原生开源软件，继承原生开源软件安全设计，不破坏原生开源软件设计及编码风格和方式，软件的任何漏洞与安全问题，均由相应的上游社区根据其漏洞和安全响应机制解决。请密切关注上游社区发布的通知和版本更新。鲲鹏计算社区对软件的漏洞及安全问题不承担任何责任。

# 许可证书<a name="ZH-CN_TOPIC_0000002442318696"></a>

本项目采用Apache License 2.0许可证。详见[LICENSE](https://gitcode.com/boostkit/libwebp/blob/master/LICENSE)文件。

