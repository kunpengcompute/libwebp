# LibWebP使用示例
以下将PNG格式图片转换成WebP格式的使用示例。

1. 创建main.cpp文件，写入以下示例代码。

    ```C++
    #include <stdio.h>
    #include <stdlib.h>
    #include <png.h>
    #include <webp/encode.h>

    unsigned char* decodePNGtoRGBA(const char* filename, int* width, int* height) {
        FILE *fp = fopen(filename, "rb");
        if (!fp) {
            perror("无法打开 PNG 文件");
            return NULL;
        }

        png_structp png = png_create_read_struct(PNG_LIBPNG_VER_STRING, NULL, NULL, NULL);
        if (!png) return NULL;

        png_infop info = png_create_info_struct(png);
        if (!info) {
            png_destroy_read_struct(&png, NULL, NULL);
            return NULL;
        }

        if (setjmp(png_jmpbuf(png))) {
            png_destroy_read_struct(&png, &info, NULL);
            fclose(fp);
            return NULL;
        }

        png_init_io(png, fp);
        png_read_info(png, info);

        *width = png_get_image_width(png, info);
        *height = png_get_image_height(png, info);
        png_byte color_type = png_get_color_type(png, info);
        png_byte bit_depth  = png_get_bit_depth(png, info);

        if (bit_depth == 16) png_set_strip_16(png);
        if (color_type == PNG_COLOR_TYPE_PALETTE) png_set_palette_to_rgb(png);
        if (color_type == PNG_COLOR_TYPE_GRAY && bit_depth < 8) png_set_expand_gray_1_2_4_to_8(png);
        if (png_get_valid(png, info, PNG_INFO_tRNS)) png_set_tRNS_to_alpha(png);
        if (color_type == PNG_COLOR_TYPE_RGB || color_type == PNG_COLOR_TYPE_GRAY || color_type == PNG_COLOR_TYPE_PALETTE)
            png_set_filler(png, 0xFF, PNG_FILLER_AFTER);
        if (color_type == PNG_COLOR_TYPE_GRAY || color_type == PNG_COLOR_TYPE_GRAY_ALPHA)
            png_set_gray_to_rgb(png);

        png_read_update_info(png, info);

        unsigned char* rgba_data = (unsigned char*)malloc((*width) * (*height) * 4);
        png_bytep* row_pointers = (png_bytep*)malloc(sizeof(png_bytep) * (*height));
        for (int y = 0; y < *height; y++) {
            row_pointers[y] = rgba_data + (y * (*width) * 4);
        }

        png_read_image(png, row_pointers);

        fclose(fp);
        free(row_pointers);
        png_destroy_read_struct(&png, &info, NULL);

        return rgba_data;
    }

    int encodeRGBAtoWebP(unsigned char* rgba_data, int width, int height, const char* output_path) {
        uint8_t* output;
        size_t output_size;

        output_size = WebPEncodeRGBA(rgba_data, width, height, width * 4, 80.0, &output);
        
        if (output_size == 0) {
            fprintf(stderr, "WebP 编码失败\n");
            return -1;
        }

        FILE* out_file = fopen(output_path, "wb");
        if (!out_file) {
            perror("无法创建输出文件");
            WebPFree(output);
            return -1;
        }

        fwrite(output, output_size, 1, out_file);
        fclose(out_file);

        printf("成功保存 WebP 至: %s (%zu 字节)\n", output_path, output_size);

        WebPFree(output);
        return 0;
    }

    int main() {
        const char* input_png = "./intput.png";    // 默认输入
        const char* output_webp = "./output.webp"; // 默认输出

        int width, height;
        unsigned char* rgba_buffer = NULL;

        printf("正在读取 PNG: %s ...\n", input_png);
        rgba_buffer = decodePNGtoRGBA(input_png, &width, &height);

        if (rgba_buffer) {
            printf("解码成功: %dx%d\n", width, height);
            
            printf("正在转换并保存为 WebP: %s ...\n", output_webp);
            if (encodeRGBAtoWebP(rgba_buffer, width, height, output_webp) != 0) {
                fprintf(stderr, "WebP 转换失败\n");
            }

            // 释放 decodePNGtoRGBA 分配的内存
            free(rgba_buffer);
        } else {
            fprintf(stderr, "读取 PNG 失败，请检查文件是否存在。\n");
            return 1;
        }

        printf("转换任务完成。\n");
        return 0;
    }
    ```

2. 编译执行。

    ```shell
    g++ -std=c++17 -I/opt/webpInstall/include -I/opt/webpInstall/include/GraphicsMagick -I/opt/webpInstall/include/webp -L/opt/webpInstall/lib -L/opt/webpInstall/lib64 -lpng -lwebp -Wl,-rpath,/opt/webpInstall/lib64  main.cpp -o main && ./main
    ```

> 预期现象：生成output.webp文件
