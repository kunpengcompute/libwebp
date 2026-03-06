# README

## libwebp

### 项目简介

本项目为鲲鹏图像库libwebp代码仓，使用向量优化，算法优化与流程优化等方法针对开源图像库libwebp图片编码性能进行了优化。

libwebp是WebP图像格式的官方库，它支持有损和无损压缩，并具备透明通道和动画功能。相比传统格式（如JPEG或PNG），WebP通常可以在保证视觉质量的前提下显著减小文件大小，是网页图像优化的理想选择。

### 目录结构

```shell
├── README.md                              # 项目说明文档
├── LICENSE                                # 文档许可证
├── docs                                   # 项目文档目录
│   └── zh                                 # 中文文档目录
├── 0001-webp_1.2.1-optimize-v1.0.1.patch  # patch文件
└── libwebp-1.2.1.tar.gz                   # libwebp-1.2.1源代码压缩包
```

### 环境部署

参考[安装指南文档](./docs/zh/installation_guide.md)。

### 快速入门

参考[快速入门文档](./docs/zh/quick_start.md)

### 贡献指导

如果使用过程中有任何问题，或者需要反馈特性需求和bug报告，可以提交issue联系我们，具体贡献方法可参考[这里](https://gitcode.com/boostkit/community/blob/master/docs/contributor/contributing.md)。

### 免责声明

此代码仓基于libwebp开源代码提供patch，仅作libwebp图片编码性能提升，编码风格遵照原生开源软件，继承原生开源软件安全设计，不破坏原生开源软件设计及编码风格和方式，软件的任何漏洞与安全问题，均由相应的上游社区根据其漏洞和安全响应机制解决。请密切关注上游社区发布的通知和版本更新。鲲鹏计算社区对软件的漏洞及安全问题不承担任何责任。

### 许可证书

本项目采用BSD 3-Clause 许可证，详见[LICENSE文件](./LICENSE)。本项目文档使用CC-BY 4.0许可证，详见[LICENSE文件](./docs/zh/LICENSE)。
