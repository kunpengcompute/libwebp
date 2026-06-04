# Example of Using LibWebP
The following example shows how to convert a PNG image to a WebP image.

1. Create the `main.cpp` file and write the following sample code into it:

    ```C++
    #include <stdio.h>
    #include <stdlib.h>
    #include <png.h>
    #include <webp/encode.h>

    unsigned char* decodePNGtoRGBA(const char* filename, int* width, int* height) {
        FILE *fp = fopen(filename, "rb");
        if (!fp) {
            perror("Failed to open the PNG file");
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
            fprintf(stderr, "Failed to encode WebP\n");
            return -1;
        }

        FILE* out_file = fopen(output_path, "wb");
        if (!out_file) {
            perror("Failed to create the output file");
            WebPFree(output);
            return -1;
        }

        fwrite(output, output_size, 1, out_file);
        fclose(out_file);

        printf("WebP saved to: %s (%zu bytes)\n", output_path, output_size);

        WebPFree(output);
        return 0;
    }

    int main() {
        const char* input_png = "./input .png";    // Default input
        const char* output_webp = "./output.webp"; // Default output

        int width, height;
        unsigned char* rgba_buffer = NULL;

        printf("Reading PNG: %s ...\n", input_png);
        rgba_buffer = decodePNGtoRGBA(input_png, &width, &height);

        if (rgba_buffer) {
            printf("Decoding successful: %dx%d\n", width, height);
            
            printf("Converting and saving as WebP: %s ...\n", output_webp);
            if (encodeRGBAtoWebP(rgba_buffer, width, height, output_webp) != 0) {
                fprintf(stderr, "WebP conversion failed\n");
            }

            // Free the memory allocated by decodePNGtoRGBA.
            free(rgba_buffer);
        } else {
            fprintf(stderr, "Failed to read PNG. Check whether the file exists.\n");
            return 1;
        }

        printf("Conversion task completed.\n");
        return 0;
    }
    ```

2. Perform the compilation.

    ```shell
    g++ -std=c++17 -I/opt/webpInstall/include -I/opt/webpInstall/include/GraphicsMagick -I/opt/webpInstall/include/webp -L/opt/webpInstall/lib -L/opt/webpInstall/lib64 -lpng -lwebp -Wl,-rpath,/opt/webpInstall/lib64  main.cpp -o main && ./main
    ```

> Expected result: The `output.webp` file is generated.
