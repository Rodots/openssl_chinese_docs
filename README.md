欢迎来到 OpenSSL 项目
========================

[![openssl logo]][www.openssl.org]

[![github actions ci badge]][github actions ci]
![Nightly OS Zoo ci badge](https://github.com/openssl/openssl/actions/workflows/os-zoo.yml/badge.svg)
![Provider Compatibility](https://github.com/openssl/openssl/actions/workflows/provider-compatibility.yml/badge.svg)
![Quic Interop](https://github.com/openssl/openssl/actions/workflows/run_quic_interop.yml/badge.svg)
![Daily checks](https://github.com/openssl/openssl/actions/workflows/run-checker-daily.yml/badge.svg)

OpenSSL 是一个强大、商业级、功能齐全的开源工具包，用于实现 TLS（以前称为 SSL）、DTLS 和 QUIC（目前仅客户端）协议。

协议实现基于一个全强度的通用加密库，该库也可以独立使用。还包括一个符合 FIPS 标准的加密模块。

OpenSSL 源自 Eric A. Young 和 Tim J. Hudson 开发的 SSLeay 库。

OpenSSL 项目的官方主页是 [www.openssl.org]。

目录
=================

- [概述](#概述)
- [下载](#下载)
- [构建和安装](#构建和安装)
- [文档](#文档)
- [许可证](#许可证)
- [支持](#支持)
- [贡献](#贡献)
- [法律事项](#法律事项)

概述
========

OpenSSL 工具包包括：

- **libssl**
  实现了所有 TLS 协议版本，最高支持 TLSv1.3（[RFC 8446]），DTLS 协议版本最高支持 DTLSv1.2（[RFC 6347]），以及 QUIC 版本 1 协议（目前仅客户端，[RFC 9000]）。

- **libcrypto**
  一个全强度的通用加密库。它是 TLS 实现的基础，但也可以独立使用。

- **openssl**
  OpenSSL 命令行工具，是一个用于加密任务、测试和分析的瑞士军刀。它可以用于：
    - 创建密钥参数
    - 创建 X.509 证书、CSR 和 CRL
    - 计算消息摘要
    - 加密和解密
    - SSL/TLS/DTLS 客户端和服务器测试
    - QUIC 客户端测试
    - 处理 S/MIME 签名或加密邮件
    - 以及更多...

下载
========

用于生产环境
------------------

可以从 [openssl-library.org/source/](https://openssl-library.org/source/) 下载官方发布的源代码压缩包。OpenSSL 项目不提供二进制形式的工具包。

然而，对于多种操作系统，预编译版本的 OpenSSL 工具包是可用的。特别是在 Linux 和其他 Unix 操作系统上，通常建议链接到由发行商或供应商提供的预编译共享库。

我们还在我们的维基页面上维护了一个第三方为各种操作系统（包括 Windows）生成 OpenSSL 二进制文件的列表，详见 [Binaries] 页面。

用于测试和开发
---------------------------

虽然测试和开发理论上也可以使用源代码压缩包进行，但拥有一个包含整个项目历史的 Git 仓库副本可以让你更深入地了解代码库。

OpenSSL 的主要 Git 仓库是私有的。在 [github.com/openssl/openssl] 上有一个公开的 GitHub 镜像，每次提交时都会自动从主仓库更新。

可以通过从 GitHub 镜像克隆来获取 Git 仓库的本地副本：

    git clone https://github.com/openssl/openssl.git

如果你打算为 OpenSSL 贡献代码，无论是修复错误还是贡献新功能，你需要 fork GitHub 镜像并克隆你的公开 fork。

    git clone https://github.com/yourname/openssl.git

这是必要的，因为现在 OpenSSL 的所有开发都是通过 GitHub 拉取请求进行的。更多详情请参见 [贡献](#贡献)。

构建和安装
=================

获取源代码后，请查看 [INSTALL](INSTALL) 文件以获取有关构建和安装 OpenSSL 的详细说明。对于某些平台，安装说明会通过特定平台的文档进行补充。

* [UNIX 类平台的说明](NOTES-UNIX.md)
* [Android 平台的说明](NOTES-ANDROID.md)
* [Windows 平台的说明](NOTES-WINDOWS.md)
* [使用 DJGPP 的 DOS 平台的说明](NOTES-DJGPP.md)
* [OpenVMS 平台的说明](NOTES-VMS.md)
* [关于 Perl 的说明](NOTES-PERL.md)
* [关于 Valgrind 的说明](NOTES-VALGRIND.md)

从之前的版本升级到 OpenSSL 3.x 的特定说明可以在 [ossl-guide-migration(7ossl)] 手册页中找到。

文档
=============

README 文件
------------

源代码分发包的顶层目录中有一些 README.md 文件，包含特定主题的附加信息。

* [关于 OpenSSL QUIC 协议实现的信息](README-QUIC.md)
* [关于 OpenSSL Provider 架构的信息](README-PROVIDERS.md)
* [关于使用 OpenSSL FIPS 验证模块的信息](README-FIPS.md)
* [关于 OpenSSL 遗留 Engine 架构的信息](README-ENGINES.md)

OpenSSL 指南
-----------------

在 [OpenSSL Guide] 中有一些关于重要 OpenSSL 主题的教程和介绍页面。

手册页
------------

主分支和所有当前稳定版本的手册页都可以在线获取。

- [OpenSSL master](https://www.openssl.org/docs/manmaster)
- [OpenSSL 3.0](https://www.openssl.org/docs/man3.0)
- [OpenSSL 3.1](https://www.openssl.org/docs/man3.1)
- [OpenSSL 3.2](https://www.openssl.org/docs/man3.2)

示例
-----

在 [demos 子文件夹](https://github.com/openssl/openssl/tree/master/demos) 中有许多使用各种 OpenSSL 功能的源代码示例。

维基
----

在 [wiki.openssl.org] 上有一个维基，目前不太活跃。它包含了很多有用的信息，但并非所有信息都是最新的。

许可证
=======

OpenSSL 采用 Apache License 2.0 许可证，这意味着只要你满足其条件，你可以自由地将其用于商业和非商业用途。

更多详情请参见 [LICENSE.txt](https://github.com/openssl/openssl/blob/master/LICENSE.txt) 文件。

支持
=======

有多种方式可以联系我们。正确的渠道取决于你的需求。更多详情请参见 [SUPPORT](SUPPORT.md) 文件。

贡献
============

如果你有兴趣并愿意为 OpenSSL 项目贡献力量，请查看 [CONTRIBUTING](CONTRIBUTING.md) 文件。

法律事项
==========

许多国家对加密的使用或出口有所限制。如果你可能受到此类限制，在尝试开发或分发加密代码之前，你应该寻求法律建议。

版权
=========

版权所有 (c) 1998-2024 The OpenSSL Project Authors

版权所有 (c) 1995-1998 Eric A. Young, Tim J. Hudson

保留所有权利。

<!-- Links  -->

[www.openssl.org]:
<https://www.openssl.org>
"OpenSSL Homepage"

[github.com/openssl/openssl]:
<https://github.com/openssl/openssl>
"OpenSSL GitHub Mirror"

[wiki.openssl.org]:
<https://wiki.openssl.org>
"OpenSSL Wiki"

[ossl-guide-migration(7ossl)]:
<https://www.openssl.org/docs/manmaster/man7/ossl-guide-migration.html>
"OpenSSL Migration Guide"

[RFC 8446]:
<https://tools.ietf.org/html/rfc8446>

[RFC 6347]:
<https://tools.ietf.org/html/rfc6347>

[RFC 9000]:
<https://tools.ietf.org/html/rfc9000>

[Binaries]:
<https://wiki.openssl.org/index.php/Binaries>
"List of third party OpenSSL binaries"

[OpenSSL Guide]:
<https://www.openssl.org/docs/manmaster/man7/ossl-guide-introduction.html>
"An introduction to OpenSSL"

<!-- Logos and Badges -->

[openssl logo]:
openssl.svg
"OpenSSL Logo"

[github actions ci badge]:
<https://github.com/openssl/openssl/workflows/GitHub%20CI/badge.svg>
"GitHub Actions CI Status"

[github actions ci]:
<https://github.com/openssl/openssl/actions?query=workflow%3A%22GitHub+CI%22>
"GitHub Actions CI"

[appveyor badge]:
<https://ci.appveyor.com/api/projects/status/8e10o7xfrg73v98f/branch/master?svg=true>
"AppVeyor Build Status"

[appveyor jobs]:
<https://ci.appveyor.com/project/openssl/openssl/branch/master>
"AppVeyor Jobs"