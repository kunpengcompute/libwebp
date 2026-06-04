# LibWebP

## Project Introduction

Kunpeng Image Library (KIL) uses methods such as vector optimization, algorithm optimization, and workflow optimization to improve the image encoding performance of LibWebP.

LibWebP is the official library of the WebP image format. It supports lossy and lossless compression, and features transparency (alpha channel) and animation. Compared with traditional formats (such as JPEG and PNG), LibWebP can significantly reduce the file size while ensuring image quality. It is an ideal choice for web page image optimization.

## Directory Structure

```shell
├── README.md                              # Project description document
├── LICENSE                                # Document license
├── docs                                   # Project document directory
│   └── en                                 # English document directory
        ├── LICENSE                        # Document license
        ├── installation_guide.md          # Deployment guide
        └── quick_start.md                 # Demo case
├── 0001-webp_1.2.1-optimize-v1.0.1.patch  # Patch file
└── libwebp-1.2.1.tar.gz                   # libwebp-1.2.1 source code package
```

## Environment Deployment

See [Installation Guide](./docs/en/installation_guide.md).

## Quick Start

See [Quick Start](./docs/en/quick_start.md).

## Contributions

If you have any questions or want to provide feedback on feature requirements and bug reports, you can submit issues. For details, see the [contribution guideline](https://gitcode.com/boostkit/community/blob/master/docs/contributor/contributing.md).

## Disclaimer

This code repository provides patches based on the open-source code of LibWebP to improve the image encoding performance of LibWebP. It strictly adheres to the coding style and methods, as well as security design of the native open-source software. Any vulnerability and security issues of the software shall be resolved by the corresponding upstream communities according to their response mechanisms. Please pay attention to the notifications and version updates released by the upstream communities. The Kunpeng computing community does not assume any responsibility for software vulnerabilities and security issues.

## License

This project is licensed under BSD 3-Clause. For details, see the [LICENSE](./LICENSE). The documents of this project are licensed under CC-BY 4.0. For details, see [LICENSE](./docs/en/LICENSE).
