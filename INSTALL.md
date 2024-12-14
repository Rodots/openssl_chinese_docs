构建和安装
============

本文档描述了在所有支持的操作系统上的安装过程：Unix/Linux 系列（包括 macOS）、OpenVMS 和 Windows。

目录
====

- [先决条件](#先决条件)
- [符号约定](#符号约定)
- [快速安装指南](#快速安装指南)
    - [构建 OpenSSL](#构建-openssl)
    - [安装 OpenSSL](#安装-openssl)
- [配置选项](#配置选项)
    - [API 级别](#api-级别)
    - [交叉编译前缀](#交叉编译前缀)
    - [构建类型](#构建类型)
    - [目录](#目录)
    - [编译器警告](#编译器警告)
    - [压缩算法标志](#压缩算法标志)
    - [随机数生成器种子](#随机数生成器种子)
    - [设置 FIPS HMAC 密钥](#设置-fips-hmac-密钥)
    - [启用和禁用功能](#启用和禁用功能)
    - [显示配置数据](#显示配置数据)
- [详细安装步骤](#详细安装步骤)
    - [配置](#配置-openssl)
    - [构建](#构建-openssl)
    - [测试](#测试-openssl)
    - [安装](#安装-openssl)
- [高级构建选项](#高级构建选项)
    - [环境变量](#环境变量)
    - [Makefile 目标](#makefile-目标)
    - [运行选定的测试](#运行选定的测试)
- [故障排除](#故障排除)
    - [配置问题](#配置问题)
    - [构建失败](#构建失败)
    - [测试失败](#测试失败)
- [注释](#注释)
    - [多线程注释](#多线程注释)
    - [共享库注释](#共享库注释)
    - [随机数生成注释](#随机数生成注释)
    - [汇编模块编译注释](#汇编模块编译注释)

先决条件
========

要安装 OpenSSL，您需要：

* 一个 "make" 实现
* Perl 5 及其核心模块（请阅读 [NOTES-PERL.md](https://github.com/openssl/openssl/blob/master/NOTES-PERL.md)）
* Perl 模块 `Text::Template`（请阅读 [NOTES-PERL.md](https://github.com/openssl/openssl/blob/master/NOTES-PERL.md)）
* 一个 ANSI C 编译器
* POSIX C 库（至少 POSIX.1-2008），或兼容的类型和功能
* 开发环境，包括开发库和 C 头文件
* 一个受支持的操作系统

有关其他平台特定的要求、特定问题的解决方案和其他详细信息，请阅读以下内容：

* [UNIX 类平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-UNIX.md)
* [Android 平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-ANDROID.md)
* [Windows 平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-WINDOWS.md)
* [使用 DJGPP 的 DOS 平台注释](https://github.com/openssl/openssl/blob/master/NOTES-DJGPP.md)
* [OpenVMS 平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-VMS.md)
* [HPE NonStop 平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-NONSTOP.md)
* [POSIX 注释](https://github.com/openssl/openssl/blob/master/NOTES-POSIX.md)
* [Perl 注释](https://github.com/openssl/openssl/blob/master/NOTES-PERL.md)
* [Valgrind 注释](https://github.com/openssl/openssl/blob/master/NOTES-VALGRIND.md)

符号约定
========

在整个文档中，我们使用以下约定。

命令
----

任何以美元符号开头的行都是命令行。

    $ command

美元符号表示 shell 提示符，不需要输入。

选择
----

用大括号和竖线分隔的几个词表示**强制选择**，需要替换为其中一个词。例如，以下行：

    $ echo { WORD1 | WORD2 | WORD3 }

表示以下三个命令之一：

    $ echo WORD1
    - 或 -
    $ echo WORD2
    - 或 -
    $ echo WORD3

用方括号和竖线分隔的一个或多个词表示**可选选择**。类似于强制选择，但也可以完全省略。

所以以下行：

    $ echo [ WORD1 | WORD2 | WORD3 ]

表示以下四个命令之一：

    $ echo WORD1
    - 或 -
    $ echo WORD2
    - 或 -
    $ echo WORD3
    - 或 -
    $ echo

参数
----

**可选参数**用方括号括起来。

    [option...]

末尾的省略号表示可以指定多个。

快速安装指南
============

如果您只想安装 OpenSSL 而不想了解太多细节，以下是构建和安装 OpenSSL 的简短版本。如果以下步骤失败，请参阅下面的 [详细安装步骤](#详细安装步骤) 部分。

构建 OpenSSL
------------

使用以下命令来配置、构建和测试 OpenSSL。测试是可选的，但如果您打算在生产环境中使用 OpenSSL，建议进行测试。

### Unix / Linux / macOS / NonStop

    $ ./Configure
    $ make
    $ make test

### OpenVMS

使用以下命令来构建 OpenSSL：

    $ perl Configure
    $ mms
    $ mms test

### Windows

如果您使用 Visual Studio，打开开发者命令提示符并发出以下命令来构建 OpenSSL。

    $ perl Configure
    $ nmake
    $ nmake test

如 [选择](#选择) 部分所述，您需要在第一个命令中选择四个 Configure 目标之一。

大多数情况下，您将使用 `VC-WIN64A`/`VC-WIN64A-HYBRIDCRT` 目标来构建 64 位 Windows 二进制文件（AMD64）或 `VC-WIN32`/`VC-WIN32-HYBRIDCRT` 来构建 32 位 Windows 二进制文件（X86）。其他两个选项是 `VC-WIN64I`（Intel IA64，Itanium）和 `VC-CE`（Windows CE），现在很少使用。

安装 OpenSSL
------------

以下命令将 OpenSSL 安装到默认的系统位置。

**危险区域：** 即使您很着急，也请仔细阅读以下两段内容后再安装 OpenSSL。

出于安全原因，默认的系统位置默认情况下对普通用户不可写。因此，最后一步需要管理员权限。默认的系统位置和获取管理员权限的过程取决于操作系统。建议使用普通用户权限编译和测试 OpenSSL，仅在最后一步使用管理员权限。

在某些平台上，OpenSSL 作为操作系统的一部分预安装。在这种情况下，强烈建议不要覆盖系统版本，因为其他应用程序或库可能依赖于它。为了避免破坏其他应用程序，请将您的 OpenSSL 副本安装到不在系统库全局搜索路径中的 [不同位置](#安装到不同位置)。

最后，如果您计划使用 FIPS 模块，您需要阅读下面的 [安装后注释](#安装后注释)。

### Unix / Linux / macOS / NonStop

根据您的发行版，您需要以 root 用户身份运行以下命令，或者在命令前加上 `sudo`：

    $ make install

默认情况下，OpenSSL 将安装到：

    /usr/local

更具体地说，文件将安装到以下子目录中：

    /usr/local/bin
    /usr/local/lib
    /usr/local/include
    ...

根据文件类型，如 Unix 类操作系统中的惯例。

### OpenVMS

使用以下命令来安装 OpenSSL。

    $ mms install

默认情况下，OpenSSL 将安装到：

    SYS$COMMON:[OPENSSL]

### Windows

如果您使用 Visual Studio，打开提升的开发者命令提示符并发出以下命令。

    $ nmake install

提升命令提示符的最简单方法是按住 `<CTRL>` 和 `<SHIFT>` 键，同时点击任务菜单中的菜单项。

默认安装位置是：

    C:\Program Files\OpenSSL

对于 64 位 Windows 上的 32 位二进制文件（WOW64），默认安装位置是：

    C:\Program Files (x86)\OpenSSL

#### 安装到不同位置

要将 OpenSSL 安装到不同位置（例如，安装到您的主目录以进行测试），请按以下示例运行 `Configure`。

选项 `--prefix` 和 `--openssldir` 在下面的 [目录](#目录) 中有更详细的解释，这里使用的值仅为示例。

在 Unix 上：

    $ ./Configure --prefix=/opt/openssl --openssldir=/usr/local/ssl

在 OpenVMS 上：

    $ perl Configure --prefix=PROGRAM:[INSTALLS] --openssldir=SYS$MANAGER:[OPENSSL]

注意：如果您在配置命令中添加了选项，请确保您不仅阅读了本快速入门，还阅读了相关的 `NOTES-*` 文件以及下面的选项概述，因为配置选项可能会以其他方式改变结果。

配置选项
========

`./Configure` 有几个选项可以自定义构建（注意，对于 Windows，`--prefix` 和 `--openssldir` 的默认值取决于使用的配置和 OpenSSL 构建的 Windows 实现。有关更多信息，请参阅 [Windows 平台的注释](https://github.com/openssl/openssl/blob/master/NOTES-WINDOWS.md)）。

API 级别
--------

    --api=x.y[.z]

构建 OpenSSL 库以支持指定版本的 API。如果同时给出 [no-deprecated](#no-deprecated)，则不构建支持指定版本或以下版本的已弃用 API。例如，添加：

    --api=1.1.0 no-deprecated

将删除对 OpenSSL 版本 1.1.0 或以下所有已弃用 API 的支持。这是一个专门用于开发人员的选项。如果您只想删除当前版本之前的所有已弃用 API，只需指定 [no-deprecated](#no-deprecated)。如果未给出 `--api`，则默认为当前（次要）OpenSSL 版本。

交叉编译前缀
------------

    --cross-compile-prefix=<PREFIX>

在工具链命令前添加 `<PREFIX>`。

它可能需要以破折号结尾，例如 `a-b-c-` 将调用 GNU 编译器为 `a-b-c-gcc` 等。不幸的是，交叉编译太具体，无法提供通用的说明。您可能需要传递更多标志或设置环境变量以使其工作。Android 和 iOS 的情况在相应的 `Configurations/15-*.conf` 文件中讨论。但在某些情况下，仅此选项就足够了。例如，在 Linux 上构建 mingw64 目标时，`--cross-compile-prefix=x86_64-w64-mingw32-` 有效。当然，前提是安装了 mingw 包。今天，Debian 和 Ubuntu 用户可以选择安装许多预打包的交叉编译器以及相应的运行时和开发包。再举一个例子，`--cross-compile-prefix=mipsel-linux-gnu-` 在这种情况下就足够了。

对于交叉编译，您必须 [手动配置](#手动配置)。此外，请注意 `--openssldir` 指的是目标文件系统，而不是您正在构建的文件系统。

构建类型
--------

    --debug

使用调试符号和零优化级别构建 OpenSSL。

    --release

不使用调试符号构建 OpenSSL。这是默认设置。

目录
----

### libdir

    --libdir=DIR

在安装目录树的顶部目录下（参见 `--prefix` 选项），库将安装到的目录名称。默认情况下，这是 `lib`。请注意，在 Windows 上，静态库（`*.lib`）将存储在此位置。共享库（`*.dll`）将始终安装到 `bin` 目录。

某些构建目标在构建配置中设置了 multilib 后缀。对于这些目标，默认的 libdir 是 `lib<multilib-postfix>`。如果添加后缀不可取，请使用 `--libdir=lib` 覆盖 libdir。

### openssldir

    --openssldir=DIR

OpenSSL 配置文件的目录，也是默认的证书和密钥存储位置。默认值为：

    Unix:           /usr/local/ssl
    Windows:        C:\Program Files\Common Files\SSL
    OpenVMS:        SYS$COMMON:[OPENSSL-COMMON]

对于 64 位 Windows 上的 32 位 Windows 应用程序（WOW64），始终将 `C:\Program Files` 替换为 `C:\Program Files (x86)`。

### prefix

    --prefix=DIR

安装目录树的顶部目录。默认值为：

    Unix:           /usr/local
    Windows:        C:\Program Files\OpenSSL
    OpenVMS:        SYS$COMMON:[OPENSSL]

编译器警告
----------

    --strict-warnings

这是一个开发者标志，用于切换各种推荐的 OpenSSL 开发编译器选项。它仅在使用 gcc 或 clang 作为编译器时有效。如果您正在为 OpenSSL 开发补丁，建议在可能的情况下使用此选项。

压缩算法标志
------------

### with-brotli-include

    --with-brotli-include=DIR

brotli 包含文件的位置目录（即 **brotli** 包含目录的位置）。仅当使用 [enable-brotli](#enable-brotli) 且包含文件不在系统包含路径中时，才需要此选项。

### with-brotli-lib

    --with-brotli-lib=LIB

**在 Unix 上**：这是包含 brotli 库的目录。如果未提供，将使用系统库路径。

库的名称是：

* libbrotlicommon.a 或 libbrotlicommon.so
* libbrotlidec.a 或 libbrotlidec.so
* libbrotlienc.a 或 libbrotlienc.so

**在 Windows 上**：这是包含 brotli 库的目录。如果未提供，将使用系统库路径。

库的名称是：

* brotlicommon.lib
* brotlidec.lib
* brotlienc.lib

### with-zlib-include

    --with-zlib-include=DIR

zlib 包含文件的位置目录。仅当使用 [zlib](#zlib) 且包含文件不在系统包含路径中时，才需要此选项。

### with-zlib-lib

    --with-zlib-lib=LIB

**在 Unix 上**：这是包含 zlib 库的目录。如果未提供，将使用系统库路径。

**在 Windows 上**：这是 zlib 库的文件名（带或不带路径）。如果未使用 [zlib-dynamic](#zlib-dynamic) 选项，则必须提供此标志。如果使用 `zlib-dynamic`，则此标志是可选的，如果未提供，则默认为 `ZLIB1`。

**在 VMS 上**：这是 zlib 库的文件名（带或不带路径）。此标志是可选的，如果未提供，则根据选择的指针大小，默认使用 `GNV$LIBZSHR`、`GNV$LIBZSHR32` 或 `GNV$LIBZSHR64`。

### with-zstd-include

    --with-zstd-include=DIR

Zstd 包含文件的位置目录。仅当使用 [enable-std](#enable-zstd) 且包含文件不在系统包含路径中时，才需要此选项。

OpenSSL 需要 Zstd 1.4 或更高版本。Linux 内核源代码包含一个与 1.4.x Zstd 发行版不兼容的 *zstd.h* 文件，如果 Linux *zstd.h* 在 Zstd 发行版头文件之前（或代替）包含，编译将生成错误。

### with-zstd-lib

    --with-zstd-lib=LIB

**在 Unix 上**：这是包含 Zstd 库的目录。如果未提供，将使用系统库路径。

**在 Windows 上**：这是 Zstd 库的文件名（带或不带路径）。如果未使用 [enable-zstd-dynamic](#enable-zstd-dynamic) 选项，则必须提供此标志。如果使用 `zstd-dynamic`，则此标志是可选的，如果未提供，则默认为 `LIBZSTD`。

随机数生成器种子
----------------

    --with-rand-seed=seed1[,seed2,...]

OpenSSL 将尝试按顺序使用这些种子方法来获取随机输入（也称为“熵”）以种子其加密安全的随机数生成器（CSPRNG）。当前的种子方法有：

### os

使用受信任的操作系统熵源。如果存在此类熵源，这是默认方法。

### getrandom

使用 [getrandom(2)][man-getrandom] 或等效的系统调用。

[man-getrandom]: http://man7.org/linux/man-pages/man2/getrandom.2.html

### devrandom

使用 `DEVRANDOM` 列表中可以打开以读取随机字节的第一个设备。在大多数类 Unix 操作系统上，`DEVRANDOM` 预处理器常量扩展为：

    "/dev/urandom","/dev/random","/dev/srandom"

### egd

检查熵生成守护进程。FIPS 提供程序将忽略此源。

### rdcpu

如果 CPU 提供，则在 x86 上使用 `RDSEED` 或 `RDRAND` 命令，在 aarch64 上使用 `RNDRRS` 命令。

### none

禁用自动种子。这是在某些操作系统上默认禁用的，因为没有合适的熵源，或者尚未实现对它的支持。FIPS 提供程序将忽略此选项。

有关更多信息，请参阅本文档末尾的 [随机数生成注释][rng] 部分。

[rng]: #随机数生成注释

### jitter

当配置为 `enable-jitter` 时，编译一个“JITTER”RNG，它可以提供一个替代的软件种子源。它可以通过在 `openssl.cnf` 中设置 `seed` 选项进行配置。一个最小的 `openssl.cnf` 如下所示：

    openssl_conf = openssl_init

    [openssl_init]
    random = random

    [random]
    seed=JITTER

它使用静态链接的 [jitterentropy-library] 作为种子源。

其他可用的配置标志：

    --with-jitter-include=DIR

如果 jitterentropy.h 文件不在系统包含路径中，则这是其位置目录。

    --with-jitter-lib=DIR

这是包含静态 libjitterentropy.a 库的目录，如果它不在系统库路径中。

设置 FIPS HMAC 密钥
-------------------

    --fips-key=value

作为自检验证的一部分，FIPS 模块必须通过对自己执行 SHA-256 HMAC 计算来验证自己。默认密钥是“holy hand grenade of antioch”的 SHA256 值，足以满足 FIPS 要求。

要更改密钥为不同的值，请使用此标志。值应为不超过 64 个字符的十六进制字符串。

启用和禁用功能
---------------

功能选项总是成对出现，一个选项启用功能 `xxxx`，另一个选项禁用它：

    [ enable-xxxx | no-xxxx ]

功能是默认启用还是禁用取决于功能。在以下列表中，始终记录非默认变体：如果功能 `xxxx` 默认禁用，则记录 `enable-xxxx`；如果功能 `xxxx` 默认启用，则记录 `no-xxxx`。

### no-afalgeng

不构建 AFALG 引擎。

此选项将在不支持 AFALG 的平台上强制启用。

### enable-ktls

构建支持内核 TLS。

此选项将启用内核 TLS 数据路径的使用，这可以提高性能，并允许在 TLS 套接字上使用 sendfile 和 splice 系统调用。内核可能会使用系统上可用的 TLS 加速器。此选项将在不支持内核 TLS 数据路径的系统上强制关闭。

### enable-asan

使用地址清理器构建。

这是一个仅限开发者的选项。它可能无法在所有平台上工作，并且永远不应在生产环境中使用。它仅在使用 gcc 或 clang 时有效，并且应与 [no-shared](#no-shared) 选项一起使用。

### enable-acvp-tests

构建支持自动化加密验证协议（ACVP）测试。

这是 FIPS 验证所必需的。某些 ACVP 测试需要访问通常不可访问的算法内部。有关 ACVP 的更多信息，请参阅 <https://github.com/usnistgov/ACVP>。

### no-apps

不构建应用程序，例如 openssl 程序。这对于最小化很有用。此选项还会禁用测试。

### no-asm

不使用汇编代码。

这应视为调试/故障排除选项，而不是用于生产。在某些平台上，即使使用此选项，仍可能会使用少量汇编代码。

### no-async

不构建异步操作支持。

### no-atexit

不在 libcrypto 构建中使用 `atexit()`。

`atexit()` 在不同平台上的语义各不相同，在某些情况下可能会导致 SIGSEGV。此选项禁用 OPENSSL_cleanup 的 atexit 注册。默认情况下，NonStop 配置使用 `no-atexit`。

### no-autoalginit

不自动加载所有支持的密码和摘要。

通常，OpenSSL 会提供其所有支持的密码和摘要。对于静态链接的应用程序，如果可执行文件大小是一个目标，这可能是不希望的。这仅影响 libcrypto。如果使用此选项，密码和摘要将需要使用 `EVP_add_cipher()` 和 `EVP_add_digest()` 手动加载。此选项将强制非共享构建。

### no-autoerrinit

不自动加载所有 libcrypto/libssl 错误字符串。

通常，OpenSSL 会自动加载人类可读的错误字符串。对于静态链接的应用程序，如果可执行文件大小是一个目标，这可能是不希望的。

### enable-brotli

构建支持 brotli 压缩/解压缩。

### enable-brotli-dynamic

类似于 enable-brotli 选项，但让 OpenSSL 在需要时动态加载 brotli 库。

这仅在支持加载共享库的系统上受支持。

### no-autoload-config

不自动加载默认的 `openssl.cnf` 文件。

通常，OpenSSL 会自动加载系统配置文件，该文件配置默认的 SSL 选项。

### enable-buildtest-c++

在测试期间，生成 C++ buildtest 文件，简单地检查公共 OpenSSL 头文件是否可以独立与 C++ 一起使用。

启用此选项需要额外注意。对于直接作为配置选项给出的任何编译器标志，您必须确保它对 C 和 C++ 编译器都有效。如果不是，C++ 构建测试很可能会失败。作为替代方案，您可以使用特定语言的变量 `CFLAGS` 和 `CXXFLAGS`。

### --banner=text

使用指定的文本而不是默认的配置结束横幅。

### --w

在未明确指定 32 位或 64 位架构选择的平台上，`Configure` 将打印警告消息并等待几秒钟，让您中断配置。使用此标志跳过等待。

### no-bulk

仅构建一些最小的功能集。

这是一个开发者选项，用于项目的 CI 构建测试。

### no-cached-fetch

当从提供程序获取算法时，从不缓存算法。通常，提供程序会指示其提供的算法是否可以缓存。使用此选项将减少运行时内存使用，但也会引入显著的性能损失。此选项主要用于帮助检测不正确的引用计数。

### no-capieng

不构建 CAPI 引擎。

此选项将在不支持 CAPI 的平台上强制启用。

### no-cmp

不构建支持证书管理协议（CMP）和证书请求消息格式（CRMF）。

### no-cms

不构建支持加密消息语法（CMS）。

### no-comp

不构建支持 SSL/TLS 压缩。

如果启用此选项（默认），则仅当同时选择 zlib 或 `zlib-dynamic` 选项时，压缩才会起作用。

### enable-crypto-mdebug

这现在仅启用 `failed-malloc` 功能。

### enable-crypto-mdebug-backtrace

这是一个无操作；项目使用编译器的地址/泄漏清理器代替。

### no-ct

不构建支持证书透明度（CT）。

### no-deprecated

不构建支持指定版本或以下版本的已弃用 API（或当前版本，如果未指定 `--api`）。

### no-dgram

不构建支持基于数据报的 BIO。

选择此选项还将强制禁用 DTLS。

### no-docs

不构建和安装文档，即各种形式的手册页。

### no-dso

不构建支持加载动态共享对象（DSO）。

### enable-devcryptoeng

构建 `/dev/crypto` 引擎。

此选项在 BSD 平台上自动选择，在这种情况下，可以使用 `no-devcryptoeng` 禁用它。

### no-dynamic-engine

不构建动态加载的引擎。

这仅在共享构建中有效果。

### no-ec

不构建支持椭圆曲线。

### no-ec2m

不构建支持二进制椭圆曲线。

### enable-ec_nistp_64_gcc_128

启用对一些常用 NIST 椭圆曲线的优化实现的支持。

此选项仅在以下平台上受支持：

- 具有小端字节序存储的非字节类型
- 容忍未对齐内存引用的平台
- 编译器：
    - 支持非标准类型 `__uint128_t`
    - 定义内置宏 `__SIZEOF_INT128__`

### enable-egd

构建支持从熵收集守护进程（EGD）收集熵。

### no-engine

不构建支持加载引擎。

### no-err

不编译任何错误字符串。

### enable-external-tests

启用与外部测试套件的集成构建。

这是一个仅限开发者的选项，可能无法在所有平台上工作。目前支持以下外部测试套件：

- GOST 引擎测试套件
- Python PYCA/Cryptography 测试套件
- krb5 测试套件

有关更多详细信息，请参阅文件 [test/README-external.md](https://github.com/openssl/openssl/blob/master/test/README-external.md)。

### no-filenames

不编译文件名和行号信息（例如，用于错误和内存分配）。

### enable-fips

构建（并安装）FIPS 提供程序。

### no-fips-securitychecks

不执行与安全参数（如密钥的最小安全强度）相关的 FIPS 模块运行时检查。

### no-fips-post

不执行 FIPS 模块开机自检。

此选项仅用于调试，因为它使 FIPS 提供程序不符合要求。在 FIPS 算法中设置断点时很有用。

### enable-fips-jitter

使用 CPU Jitter 库作为 FIPS 验证的熵源。

仅当您：

1. 独立执行所需的 [SP 800-90B] 熵评估；
2. 满足 [jitterentropy-library] 指定的最小所需熵；
3. 获得 [jitterentropy-library] 的 [ESV] 证书；
4. 通过 [CMVP] 认证生成的 FIPS 提供程序。

未能做到这些将生成一个不符合要求的 FIPS 提供程序。

### enable-fuzz-libfuzzer, enable-fuzz-afl

使用 libfuzzer 或 AFL 构建支持模糊测试。

这些是仅限开发者的选项。它们可能无法在所有平台上工作，并且永远不应在生产环境中使用。

有关更多详细信息，请参阅文件 [fuzz/README.md](https://github.com/openssl/openssl/blob/master/fuzz/README.md)。

### no-gost

不构建支持基于 GOST 的密码套件。

请注意，如果启用此功能，则仅当通过加载外部提供的引擎也可用 GOST 算法时，GOST 密码套件才可用。

### no-http

禁用 HTTP 支持。

### no-legacy

不构建遗留提供程序。

禁用此功能还会禁用遗留算法：MD2（已默认禁用）。

### no-makedepend

不生成依赖项。

### no-module

不构建任何动态加载的引擎。

这也意味着 `no-dynamic-engine`。

### no-multiblock

不构建支持在 libssl 中一次写入多个记录。

注意：这与流水线功能不同。

### no-nextprotoneg

不构建支持下一个协议协商（NPN）TLS 扩展。

### no-ocsp

不构建支持在线证书状态协议（OCSP）。

### no-padlockeng

不构建 padlock 引擎。

### no-hw-padlock

作为 `no-padlockeng` 的同义词。已弃用，不应使用。

### no-pic

不构建支持位置无关代码。

### enable-pie

构建支持位置无关执行。

### no-pinshared

不固定共享库。

默认情况下，OpenSSL 将尝试在进程退出之前保持在内存中。这是为了让 libcrypto 和 libssl 可以通过 `atexit()` 处理程序自动清理。处理程序由 libcrypto 注册，并清理两个库。在某些平台上，`atexit()` 处理程序将在卸载 libcrypto 时运行（如果它已动态加载），而不是在进程退出时运行。

此选项可用于阻止 OpenSSL 尝试在进程退出之前保持在内存中。如果 libcrypto 或 libssl 在调用 atexit 处理程序时已经卸载，这可能会导致崩溃，例如在调用 `atexit()` 时卸载库的平台上，并且 libssl 在 libcrypto 之前卸载，则很可能会发生崩溃。

请注意，共享库固定不会自动为静态构建禁用，即 `no-shared` 并不意味着 `no-pinshared`。当将 libcrypto 静态链接到共享的第三方库时，这可能会令人惊讶，因为在这种情况下，共享库将被固定。要防止此行为，您需要使用 `no-shared` 和 `no-pinshared` 一起配置静态构建。

应用程序可以通过使用 `OPENSSL_INIT_NO_ATEXIT` 选项调用 `OPENSSL_init_crypto()` 来抑制 `atexit()` 处理程序的运行。有关更多详细信息，请参阅其手册页。

### no-posix-io

不使用 POSIX IO 功能。

### no-psk

不构建支持基于预共享密钥的密码套件。

### no-rdrand

不使用硬件 RDRAND 功能。

### no-rfc3779

不构建支持 RFC3779，“X.509 扩展用于 IP 地址和 AS 标识符”。

### sctp

构建支持流控制传输协议（SCTP）。

### no-shared

不创建共享库，仅创建静态库。

请参阅下面的 [共享库注释](#共享库注释)。

### no-sm2-precomp

在 aarch64 上禁用使用 SM2 预计算表以使库更小。

### no-sock

不构建支持套接字 BIO。

### no-srp

不构建支持安全远程密码（SRP）协议或基于 SRP 的密码套件。

### no-srtp

不构建支持安全实时传输协议（SRTP）。

### no-sse2

从 32 位 x86 汇编模块中排除 SSE2 代码路径。

通常，SSE2 扩展在运行时检测，但决定是否执行机器代码仅基于 CPU 能力向量。这意味着如果您在运行不支持 SSE2 扩展的 OS 内核的 Intel P4 处理器上运行应用程序，则可能会遇到“非法指令”异常。可能有一种方法在内核中启用支持，例如 FreeBSD 内核可以编译为 `CPU_ENABLE_SSE`，并且有一种方法在应用程序启动时禁用 SSE2 代码路径，但如果您针对运行此类内核的更广泛的“受众”，请考虑 `no-sse2`。`386` 和 `no-asm` 选项都意味着 `no-sse2`。

### no-ssl-trace

不构建支持 SSL 跟踪功能。

这将删除 `s_client` 和 `s_server` 的 `-trace` 选项，并从 libssl 中省略 `SSL_trace()` 函数。

禁用 `ssl-trace` 可能会略微减少 libssl 二进制文件的大小。

### no-static-engine

不构建静态链接的引擎。

这仅在非共享构建中有影响。

### no-stdio

不使用 C 头文件 `stdio.h` 中的任何内容，这些内容使用了 `FILE` 类型。仅 libcrypto 和 libssl 可以这种方式构建。使用此选项将阻止构建命令行应用程序。此外，由于 OpenSSL 测试也使用命令行应用程序，测试也将被跳过。

### no-tests

不构建测试程序或运行任何测试。

### enable-tfo

构建支持 TCP 快速打开（RFC7413）。在 Linux、macOS 和 FreeBSD 上受支持。

### no-quic

不构建支持 QUIC。

### no-threads

不构建支持多线程应用程序。

### threads

构建支持多线程应用程序。大多数平台将默认启用此功能。但是，如果在此类平台上未启用此功能，则通常需要额外的系统依赖选项！

请参阅下面的 [多线程注释](#多线程注释)。

### no-thread-pool

不构建支持线程池功能。

### thread-pool

构建支持线程池功能。如果启用，OpenSSL 算法可能会使用线程池进行并行计算。此选项本身不会使 OpenSSL 生成新线程。目前唯一支持的线程池机制是默认线程池。

### no-default-thread-pool

不构建支持默认线程池功能。

### default-thread-pool

构建支持默认线程池功能。如果启用，OpenSSL 可能会创建和管理线程，直到应用程序授权的最大线程数。在符合 POSIX 的平台和 Windows 上受支持。

### enable-trace

构建支持集成跟踪 API。

有关详细信息，请参阅手册页 OSSL_trace_set_channel(3) 和 OSSL_trace_enabled(3)。

### enable-sslkeylog

构建支持 SSLKEYLOGFILE 环境变量。

启用后，将 SSLKEYLOGFILE 设置为文件路径将记录在 TLS 握手期间交换的密钥，以便在分析工具（如 wireshark）中使用。请注意，使用此机制允许使用密钥日志文件解密捕获数据包中的应用程序有效载荷，因此具有显著的安全后果。请参阅 [SSLKEYLOGFILE 草案标准](https://datatracker.ietf.org/doc/draft-ietf-tls-keylogfile/) 的第 3 节。

### no-ts

不构建时间戳（TS）授权支持。

### enable-ubsan

使用未定义行为清理器（UBSAN）构建。

这是一个仅限开发者的选项。它可能无法在所有平台上工作，并且永远不应在生产环境中使用。它仅在使用 gcc 或 clang 时有效，并且应与 `-DPEDANTIC` 选项（或 `--strict-warnings` 选项）一起使用。

### no-ui-console

不构建支持用户界面（UI）控制台方法。

用户界面控制台方法启用基于文本的控制台提示。

### enable-unit-test

启用额外的单元测试 API。

这通常不应在生产部署中使用。

### no-uplink

不构建支持 UPLINK 接口。

### enable-weak-ssl-ciphers

构建支持被认为是“弱”的 SSL/TLS 密码套件。

启用此选项包括例如基于 RC4 的密码套件。

### zlib

构建支持 zlib 压缩/解压缩。

### zlib-dynamic

类似于 zlib 选项，但让 OpenSSL 在需要时动态加载 zlib 库。

这仅在支持加载共享库的系统上受支持。

### enable-zstd

构建支持 Zstd 压缩/解压缩。

### enable-zstd-dynamic

类似于 enable-zstd 选项，但让 OpenSSL 在需要时动态加载 Zstd 库。

这仅在支持加载共享库的系统上受支持。

### enable-unstable-qlog

为 QUIC 协议启用 qlog 输出支持。此功能不稳定，实现了 qlog 规范的草案版本。OpenSSL 的 qlog 输出将在未来以不兼容的方式更改，并且不受任何格式稳定性或兼容性保证的约束。有关详细信息，请参阅手册页 openssl-qlog(7)。

### 386

在 32 位 x86 构建中，仅在汇编模块中使用 80386 指令集。

默认的 x86 代码更高效，但至少需要 486 处理器。注意：这不影响编译器生成的代码，因此此选项需要伴随相应的编译器特定选项。

### no-{protocol}

    no-{ssl|ssl3|tls|tls1|tls1_1|tls1_2|tls1_3|dtls|dtls1|dtls1_2}

不构建支持协商指定 SSL/TLS 协议。

如果选择 `no-tls`，则所有 `tls1`、`tls1_1`、`tls1_2` 和 `tls1_3` 都将被禁用。类似地，`no-dtls` 将禁用 `dtls1` 和 `dtls1_2`。`no-ssl` 选项与 `no-ssl3` 同义。请注意，这仅影响版本协商。OpenSSL 仍将为应用程序提供显式选择各个协议版本的方法。

### no-integrity-only-ciphers

不构建支持 tls 中的完整性仅密码。

### no-{protocol}-method

    no-{ssl3|tls1|tls1_1|tls1_2|dtls1|dtls1_2}-method

类似于 `no-{protocol}`，但此外不构建应用程序显式选择各个协议版本的方法。请注意，没有 `no-tls1_3-method` 选项，因为 TLSv1.3 没有应用程序方法。

直接使用各个协议方法已弃用。应用程序应使用 `TLS_method()` 代替。

### enable-{algorithm}

    enable-{md2|rc5}

构建支持指定算法。

### no-{algorithm}

    no-{aria|bf|blake2|camellia|cast|chacha|cmac|
        des|dh|dsa|ecdh|ecdsa|idea|md4|mdc2|ocb|
        poly1305|rc2|rc4|rmd160|scrypt|seed|
        siphash|siv|sm2|sm3|sm4|whirlpool}

不构建支持指定算法。

`ripemd` 算法已弃用，如果使用，与 `rmd160` 同义。

### 编译器特定选项

    -Dxxx, -Ixxx, -Wp, -lxxx, -Lxxx, -Wl, -rpath, -R, -framework, -static

这些系统特定选项将被识别并传递给编译器，以允许您定义预处理器符号、指定附加库、库目录或其他编译器选项。可能需要注意的是，某些编译器会为编译器当前执行的处理器生成特定代码。这不一定符合您的预期，因为它可能不适合在其他（通常是较旧的）处理器上执行。请查阅您的编译器文档。

请注意下面的 [环境变量](#环境变量) 文档，以及这些标志如何与这些变量交互。

    -xxx, +xxx, /xxx

其他未被识别的选项将按原样传递给编译器。以 `-` 或 `+` 开头的 Unix 风格选项和以 `/` 开头的 Windows 风格选项将被识别。再次查阅您的编译器文档。

如果选项包含由空格分隔的参数，则可以使用 URL 风格的 `%20` 表示空格字符，以避免必须引用选项。例如，`-opt%20arg` 将扩展为 `-opt arg`。实际上，任何 ASCII 字符都可以使用其十六进制编码作为 %xx 进行编码。

请注意下面的 [环境变量](#环境变量) 文档，以及这些标志如何与这些变量交互。

### 环境变量

    VAR=value

将给定值分配给环境变量 `VAR` 以供 `Configure` 使用。

这些工作方式与普通环境变量分配相同，但在所有平台上都受支持，并且仅限于配置脚本。如果继承的环境中有相应的值，这些分配将覆盖该值。

以下变量用作“`make` 变量”，可以用作直接作为配置选项提供预处理器、编译器和链接器选项的替代方法。支持以下变量：

    AR              静态库归档器。
    ARFLAGS         静态库归档器的标志。
    AS              汇编编译器。
    ASFLAGS         汇编编译器的标志。
    CC              C 编译器。
    CFLAGS         C 编译器的标志。
    CXX             C++ 编译器。
    CXXFLAGS       C++ 编译器的标志。
    CPP             C/C++ 预处理器。
    CPPFLAGS       C/C++ 预处理器的标志。
    CPPDEFINES      以平台特定字符（Unix 为 ':' 或空格，Windows 为 ';'，VMS 为 ','）分隔的 CPP 宏定义列表。这可以代替在 CPPFLAGS 中使用 -D（或您的编译器对应的内容）。
    CPPINCLUDES     以与 CPPDEFINES 相同的方式分隔的 CPP 包含目录列表。这可以代替在 CPPFLAGS 中使用 -I（或您的编译器对应的内容）。
    HASHBANGPERL    在公共 perl 脚本中插入的 Perl 调用，位于 '#!' 之后（仅在 Unix 上相关）。
    LD              程序链接器（在 Unix 上不使用，$(CC) 在那里使用）。
    LDFLAGS         共享库、DSO 和程序链接器的标志。
    LDLIBS          链接时使用的额外库。在 Unix 和 Windows 上以空格分隔的库规范列表，在 VMS 上以逗号分隔的库列表。
    RANLIB          库归档索引器。
    RC              Windows 资源编译器。
    RCFLAGS         Windows 资源编译器的标志。
    RM              删除文件和目录的命令。

这些不能与在命令行上给出的编译/链接标志混合使用。换句话说，以下内容是不允许的：

    $ ./Configure -DFOO CPPFLAGS=-DBAR -DCOOKIE

向后兼容性说明：

为了与旧的配置脚本兼容，如果在命令行上给出了编译/链接标志，则忽略环境变量，除了以下变量：

    AR, CC, CXX, CROSS_COMPILE, HASHBANGPERL, PERL, RANLIB, RC, 和 WINDRES

例如，以下命令将看不到 `-DBAR`：

    $ CPPFLAGS=-DBAR ./Configure -DCOOKIE

但是，以下命令将看到两个设置的变量：

    $ CC=gcc CROSS_COMPILE=x86_64-w64-mingw32- ./Configure -DCOOKIE

如果设置了 `CC`，建议也设置 `CXX`，以确保 C 和 C++ 编译器在同一“系列”中。这在使用 `enable-external-tests` 和 `enable-buildtest-c++` 时变得相关。

### 重新配置

    reconf
    reconfigure

从之前的数据重新配置。

这将获取保存在 `configdata.pm` 中的先前命令行选项和环境，并使用这些选项和环境再次运行配置过程。注意：NO 其他选项与 `reconf` 一起使用。注意：原始配置保存了所有使用的环境变量的值，如果它们未定义，则仍会保存未定义的信息。此信息优先于重新配置时定义的环境变量。

显示配置数据
------------

配置脚本本身不会说太多，而是通过创建 `configdata.pm` 来完成。此 perl 模块可以被其他脚本加载以查找所有配置数据，也可以作为脚本运行以以人类可读的形式显示各种配置数据。

有关更多信息，请执行以下操作：

    $ ./configdata.pm --help                         # Unix

或

    $ perl configdata.pm --help                      # Windows 和 VMS

详细安装步骤
============

配置 OpenSSL
------------

### 自动配置

在以前的版本中，`config` 脚本确定平台类型和编译器，然后调用 `Configure`。从 3.0 版本开始，它们是相同的。

#### Unix / Linux / macOS

    $ ./Configure [options...]

#### OpenVMS

    $ perl Configure [options...]

#### Windows

    $ perl Configure [options...]

### 手动配置

OpenSSL 知道许多不同的操作系统、硬件和编译器组合。要查看它知道的内容，请运行：

    $ ./Configure LIST                               # Unix

或

    $ perl Configure LIST                            # 所有其他平台

对于本文的其余部分，所有示例都将使用 Unix 形式。请为您的平台使用适当的形式。

从列表中选择一个适合您的系统的名称。对于大多数操作系统，您可以选择使用 cc 或 gcc。当您确定您的系统（以及必要的编译器）后，使用此名称作为 `Configure` 的参数。例如，`linux-elf` 用户将运行：

    $ ./Configure linux-elf [options...]

### 创建您自己的配置

如果您的系统未列出，您需要创建一个名为 `Configurations/YOURFILENAME.conf` 的配置文件（将 `YOURFILENAME` 替换为您选择的文件名），并为您系统添加正确的配置。请参考现有的配置文件作为示例，并阅读 [Configurations/README.md](https://github.com/openssl/openssl/blob/master/Configurations/README.md) 和 [Configurations/README-design.md](https://github.com/openssl/openssl/blob/master/Configurations/README-design.md) 以获取更多信息。

通用的配置文件 `cc` 或 `gcc` 通常适用于 32 位类 Unix 系统。

`Configure` 会根据 `Configurations/` 中的合适模板创建一个构建文件（在 Unix 上是 `Makefile`，在 Windows 上是 `makefile`，在 OpenVMS 上是 `descrip.mms`），并在 `include/openssl/configuration.h` 中定义各种宏（从 `include/openssl/configuration.h.in` 生成）。

如果没有生成的构建文件适合您的需求，您可以编写自己的构建文件模板，并通过环境变量 `BUILDFILE` 指定其名称。例如，可以通过编写 `Configurations/build.ninja.tmpl` 来支持 Ninja 构建文件，然后使用 `BUILDFILE` 进行配置（以下显示 Unix 语法，您需要根据其他平台进行调整）：

    $ BUILDFILE=build.ninja perl Configure [选项...]

### 外部构建

OpenSSL 可以配置为在源代码目录之外的构建目录中进行构建。通过将自己置于某个其他目录中并从该目录调用配置命令来完成此操作。

#### Unix 示例

    $ mkdir /var/tmp/openssl-build
    $ cd /var/tmp/openssl-build
    $ /PATH/TO/OPENSSL/SOURCE/Configure [选项...]

#### OpenVMS 示例

    $ set default sys$login:
    $ create/dir [.tmp.openssl-build]
    $ set default [.tmp.openssl-build]
    $ perl D:[PATH.TO.OPENSSL.SOURCE]Configure [选项...]

#### Windows 示例

    $ C:
    $ mkdir \temp-openssl
    $ cd \temp-openssl
    $ perl d:\PATH\TO\OPENSSL\SOURCE\Configure [选项...]

路径可以是相对的，也可以是绝对的。`Configure` 会尽可能将其转换为相对路径。

### 构建 OpenSSL

通过运行以下命令来构建 OpenSSL：

    $ make                                           # Unix
    $ mms                                            ! (或 mmk) OpenVMS
    $ nmake                                          # Windows

这将构建 OpenSSL 库（在 Unix 上是 `libcrypto.a` 和 `libssl.a`，在其他平台上对应）和 OpenSSL 二进制文件（`openssl`）。库将构建在顶层目录中，二进制文件将在 `apps/` 子目录中。

如果构建失败，请查看 [故障排除](#故障排除) 部分的 [构建失败](#构建失败) 子节。

### 测试 OpenSSL

在成功构建后，安装之前，应测试库。运行：

    $ make test                                      # Unix
    $ mms test                                       ! OpenVMS
    $ nmake test                                     # Windows

**警告：** 您必须从非特权账户运行测试（或者如果您的平台允许，暂时禁用您的权限）。

有关如何运行测试的更多详细信息，请参阅 [test/README.md](https://github.com/openssl/openssl/blob/master/test/README.md)。

有关添加测试的指南，请参阅 [test/README-dev.md](https://github.com/openssl/openssl/blob/master/test/README-dev.md)。

### 安装 OpenSSL

如果一切测试通过，请使用以下命令安装 OpenSSL：

    $ make install                                   # Unix
    $ mms install                                    ! OpenVMS
    $ nmake install                                  # Windows

请注意，为了执行上述安装步骤，您需要有写入安装目录的适当权限。

上述命令将在此目录树下安装所有软件组件，位于 `<PREFIX>`（由 `--prefix` 指定或其默认值）：

### Unix / Linux / macOS

    bin/           包含 openssl 二进制文件和一些其他实用脚本。
    include/openssl 包含构建使用 libcrypto 或 libssl 的程序所需的头文件。
    lib            包含 OpenSSL 库文件。
    lib/engines    包含 OpenSSL 动态加载引擎。

    share/man/man1 包含 OpenSSL 命令行手册页。
    share/man/man3 包含 OpenSSL 库调用手册页。
    share/man/man5 包含 OpenSSL 配置格式手册页。
    share/man/man7 包含 OpenSSL 其他杂项手册页。

    share/doc/openssl/html/man1
    share/doc/openssl/html/man3
    share/doc/openssl/html/man5
    share/doc/openssl/html/man7 包含手册页的 HTML 版本。

### OpenVMS

'arch' 替换为架构名称，`ALPHA` 或 `IA64`，'sover' 替换为共享库版本（1.1 为 `0101`），'pz' 替换为 OpenSSL 构建时使用的指针大小：

    [.EXE.'arch']  包含 openssl 二进制文件。
    [.EXE]         包含一些实用脚本。
    [.include.openssl] 包含构建使用 libcrypto 或 libssl 的程序所需的头文件。
    [.LIB.'arch']  包含 OpenSSL 库文件。
    [.ENGINES'sover''pz'.'arch'] 包含 OpenSSL 动态加载引擎。
    [.SYS$STARTUP] 包含启动、登录和关闭脚本。这些定义了适当的逻辑名称和命令符号。
    [.SYSTEST]     包含安装验证程序。
    [.HTML]        包含手册页的 HTML 版本。

### 其他目录

此外，安装将在 OPENSSLDIR（由 `--openssldir` 指定或其默认值）下添加以下目录以方便使用：

    certs          初始为空，这是证书文件的默认位置。
    private        初始为空，这是私钥文件的默认位置。
    misc           各种脚本。

安装目录应适当保护，以确保非特权用户无法更改 OpenSSL 二进制文件或文件，或安装引擎。如果您已经有一个作为操作系统一部分的预安装 OpenSSL 版本，建议您不要覆盖系统版本，而是安装到其他位置。

希望为标准位置配置库但将包安装到其他位置以便轻松打包的包构建者可以使用：

    $ make DESTDIR=/tmp/package-root install         # Unix
    $ mms/macro="DESTDIR=TMP:[PACKAGE-ROOT]" install ! OpenVMS

指定的目标目录将添加到所有安装目标路径之前。

### 与之前 OpenSSL 版本的兼容性问题

#### 编译现有应用程序

从 1.1.0 版本开始，OpenSSL 隐藏了许多以前公开的结构。这包括所有内部 libssl 结构和一些 EVP 类型。已添加访问器函数以允许对结构数据进行受控访问。

这意味着一些软件需要重写以适应新的做事方式。这通常意味着在以前可以自动变量分配结构的地方显式分配结构实例，并使用提供的访问器函数，而不是直接访问结构字段。

一些 API 也发生了变化。然而，旧的 API 在可能的情况下被保留。

### 安装后说明

默认的 OpenSSL 安装附带一个 FIPS 提供程序模块，该模块需要一些安装后操作，否则将无法使用。这涉及使用以下命令：

    $ openssl fipsinstall

有关详细信息和示例，请参阅 openssl-fipsinstall(1) 手册。

### 高级构建选项

#### 环境变量

许多环境变量可用于提供对构建过程的额外控制。通常应在运行 `Configure` 之前定义这些变量。并非所有环境变量都与所有平台相关。

    AR              要使用的 ar 可执行文件的名称。

    BUILDFILE       使用不同于平台默认的构建文件名（在类 Unix 平台上为 "Makefile"，在 Windows 上为 "makefile"，在 OpenVMS 上为 "descrip.mms"）。这需要存在相应的构建文件模板。
                    有关更多信息，请参阅 [Configurations/README.md](Configurations/README.md)。

    CC              要使用的编译器。Configure 将尝试为您的平台选择默认编译器，但可以使用此变量覆盖该选择。将其设置为您希望使用的编译器可执行文件，例如 gcc 或 clang。

    CROSS_COMPILE   此环境变量与 "--cross-compile-prefix" Configure 标志具有相同含义。如果两者都设置，则 Configure 标志优先。

    HASHBANGPERL    要插入到将公开安装的 perl 脚本的 #! 行中的 Perl 可执行文件的命令字符串。
                    默认值：/usr/bin/env perl
                    注意：此变量的值将添加到所有平台的相同脚本中，但仅在类 Unix 平台上相关。

    KERNEL_BITS     这可以是值 `32` 或 `64`，以指定架构，当它对配置来说不“明显”时。通常不需要指定此环境变量。

    NM              要使用的 nm 可执行文件的名称。

    OPENSSL_LOCAL_CONFIG_DIR  OpenSSL 附带了一个关于如何在不同平台上构建的信息数据库，以及这些平台的构建文件模板。数据库由 Configurations 目录中的 ".conf" 文件组成。构建文件模板也位于那里，作为 ".tmpl" 文件。有关 ".conf" 文件的格式以及 ".tmpl" 文件的信息，请参阅文件 [Configurations/README.md](Configurations/README.md)。
                    除了标准的 ".conf" 和 ".tmpl" 文件外，还可以创建自己的 ".conf" 和 ".tmpl" 文件，并将它们存储在 OpenSSL 源代码树之外的本地。此环境变量可以设置为这些文件所在的目录，Configure 将在标准目录之前考虑它。

    PERL            构建 OpenSSL 时要使用的 Perl 可执行文件的名称。仅在构建应使用与运行 Configure 脚本不同的 Perl 可执行文件时需要。

    RANLIB          要使用的 ranlib 可执行文件的名称。

    RC              要使用的 rc 可执行文件的名称。默认值将如 ".conf" 文件中为目标平台定义的那样。如果未定义，则将使用 "windres"。WINDRES 环境变量与此相同。如果两者都定义，则 RC 优先。

    WINDRES         见 RC。

#### Makefile 目标

`Configure` 脚本生成的 Makefile 格式与特定平台相关。Makefiles 提供了许多目标，可用于构建过程。并非所有目标都可能在所有平台上可用。这里仅描述最常见的目标。请检查 Makefiles 本身以获取完整列表。

    all             构建所有软件组件和文档。

    build_sw        构建所有软件组件。
                    这是默认目标。

    build_docs      构建所有文档组件。

    debuginfo       在 Unix 平台上，此目标可用于创建 .debug 库，这些库将共享库 ELF 文件中的 DWARF 信息分离到单独的文件中，以便用于事后调试。

    clean           删除所有构建工件并返回目录到“干净”状态。

    depend          重新构建 Makefiles 中的依赖项。这是一个遗留选项，自 OpenSSL 1.1.0 起不再需要使用。

    install         安装所有 OpenSSL 组件。

    install_sw      仅安装 OpenSSL 软件组件。

    install_docs    仅安装 OpenSSL 文档组件。

    install_man_docs 仅安装 OpenSSL 手册页（仅限 Unix）。

    install_html_docs 仅安装 OpenSSL HTML 文档。

    install_fips    安装 FIPS 提供程序模块配置文件。

    list-tests      打印所有自测名称的列表。

    test            构建并运行 OpenSSL 自测。

    uninstall       卸载所有 OpenSSL 组件。

    reconfigure     重新运行配置过程，尽可能与上次完全相同。

    update          这是一个开发者选项。如果您正在为 OpenSSL 开发补丁，您可能需要使用此选项来更新自动生成的文件；添加新的错误代码或添加新的（或更改可见性的）公共 API 函数。（仅限 Unix）。

### 运行选定的测试

您可以使用 `make` 变量 `TESTS` 指定要执行的测试集。

有关更多信息，请参阅 [test/README.md](https://github.com/openssl/openssl/blob/master/test/README.md#running-selected-tests) 的 [运行选定的测试] 部分。

### 故障排除

#### 配置问题

##### 选择正确的目标

`./Configure` 脚本会尽力猜测您的操作系统，但在某些情况下可能无法成功。您将看到如下消息：

    $ ./Configure
    Operating system: x86-whatever-minix
    This system (minix) is not supported. See file INSTALL.md for details.

即使 `./Configure` 脚本自动目标选择失败，您仍然可能在 `Configurations` 目录中找到合适的目标，您可以将其提供给 `./Configure` 命令，可能需要进行一些调整。

`Configurations/` 目录包含许多此类目标的示例。主配置文件是 [10-main.conf]，其中包含 OpenSSL 团队正式支持的所有目标。其他配置文件包含其他 OpenSSL 用户贡献的目标。目标列表可以在 Perl 列表 `my %targets = ( ... )` 中找到。

    my %targets = (
    ...
    "target-name" => {
        inherit_from     => [ "base-target" ],
        CC               => "...",
        cflags           => add("..."),
        asm_arch         => '...',
        perlasm_scheme   => "...",
    },
    ...
    )

如果您不带参数调用 `./Configure`，它将为您列出所有已知目标。使用 `grep`，您可以在 `Configurations/` 目录中查找目标定义。例如，`android-x86_64` 可以在 [Configurations/15-android.conf](https://github.com/openssl/openssl/blob/master/Configurations/15-android.conf) 中找到。

该目录包含两个 README 文件，解释了配置文件的一般语法和设计。

- [Configurations/README.md](https://github.com/openssl/openssl/blob/master/Configurations/README.md)
- [Configurations/README-design.md](https://github.com/openssl/openssl/blob/master/Configurations/README-design.md)

如果您需要进一步帮助，请尝试搜索 [openssl-users] 邮件列表或 [GitHub Issues] 以查找现有解决方案。如果您找不到任何内容，您可以 [提出问题] 以自己提问。

有关我们的支持资源的更多信息，请参阅 [SUPPORT] 文件。

##### 配置错误

如果 `./config` 或 `./Configure` 命令失败并显示错误消息，请仔细阅读错误消息并尝试找出您是否犯了错误（例如，提供了错误选项），或者脚本是否工作不正确。如果您认为遇到了错误，请在 GitHub 上 [提出问题] 以提交错误报告。

除了对错误的简短描述外，请提供完整的配置命令行和相关的输出，包括错误消息。

注意：为了使输出可读，请在输出前后添加“代码围栏”（三个反引号 ` ``` ` 单独一行）：

     ```
     ./Configure [您的参数...]

     [输出...]

     ```

#### 构建失败

如果构建失败，请仔细查看输出。尝试定位并理解错误消息。可能是编译器已经告诉您需要做什么来修复问题。

可能存在导致失败的原因不是 OpenSSL 本身的问题，例如编译器报告缺少标准或第三方头文件。

如果构建之前成功，但在源代码或配置更改后失败，可能有助于在尝试再次构建之前清理构建树。使用以下命令：

    $ make clean                                     # Unix
    $ mms clean                                      ! (或 mmk) OpenVMS
    $ nmake clean                                    # Windows

汇编器错误消息有时可以通过使用 `no-asm` 配置选项来规避。另请参阅 [关于汇编器模块编译的说明](关于汇编器模块编译的说明)。

使用 gcc 编译 OpenSSL 的一部分，而使用系统编译器编译其他部分，在某些系统上会导致未解析的符号。

如果您仍然遇到问题，请尝试搜索 [openssl-users] 邮件列表或 [GitHub Issues] 以查找现有解决方案。如果您认为遇到了 OpenSSL 错误，请 [提出问题] 以提交错误报告。请花时间先查看现有问题；可能错误已经报告或已经修复。

#### 测试失败

如果某些测试失败，请查看输出。可能存在导致失败的原因不是 OpenSSL 本身的问题（例如操作系统故障或 Perl 问题）。

您可能希望增加详细程度，这可以通过 [test/README.md](https://github.com/openssl/openssl/blob/master/test/README.md#test-failures) 的 [测试失败] 部分中描述的方式实现。

您还可以选择性地指定要执行的测试。这可以使用 `make` 变量 `TESTS` 来完成，如 [test/README.md](https://github.com/openssl/openssl/blob/master/test/README.md#running-selected-tests) 的 [运行选定的测试] 部分所述。

如果您发现 OpenSSL 本身的问题，请尝试从 Makefile 中的 `CFLAGS` 行中删除任何编译器优化标志，并运行 `make clean; make` 或相应的命令。

要报告错误，请在 GitHub 上打开问题，地址为 <https://github.com/openssl/openssl/issues>。

### 说明

#### 关于多线程的说明

对于某些系统，OpenSSL `Configure` 脚本知道需要哪些编译器选项来生成适合多线程应用程序的库。在这些系统上，默认启用多线程支持；使用 `no-threads` 选项禁用（这通常不需要）。

在其他系统上，要启用多线程支持，您至少需要指定两个选项：`threads` 和系统相关的选项（例如，在各种系统上为 `-D_REENTRANT`）。在这种情况下，默认情况下不包含多线程支持（但您仍然可以使用 `no-threads` 来抑制 `Configure` 脚本中的警告消息）。

OpenSSL 提供了对两种线程模型的内置支持：pthreads（在大多数 UNIX/Linux 系统上找到）和 Windows 线程。不支持其他线程模型。如果您的平台不提供 pthreads 或 Windows 线程，则应使用 `Configure` 脚本并带上 `no-threads` 选项。

对于 pthreads，所有锁都是非递归的。此外，在调试构建中，使用 `PTHREAD_MUTEX_ERRORCHECK` 互斥属性。如果您的平台上没有此属性，您可能需要在 `Configure` 调用中添加 `-DOPENSSL_NO_MUTEX_ERRORCHECK`。（在 Linux 上，`PTHREAD_MUTEX_ERRORCHECK` 是一个枚举值，因此无法使用内置的 ifdef 测试。）

#### 关于共享库的说明

对于大多数系统，OpenSSL `Configure` 脚本知道如何构建 libcrypto 和 libssl 的共享库。在这些系统上，默认情况下会创建共享库。这可以通过使用 `no-shared` 选项来抑制，并且只创建静态库。在 OpenSSL 不知道如何构建共享库的系统上，将强制使用 `no-shared` 选项，并且只创建静态库。

共享库在不同平台上的命名略有不同。无论如何，它们都包含 OpenSSL 的主版本号作为文件名的一部分，即对于 OpenSSL 1.1.x，`1.1` 是名称的一部分。

在大多数 POSIX 平台上，共享库命名为 `libcrypto.so.1.1` 和 `libssl.so.1.1`。

在 Cygwin 上，共享库命名为 `cygcrypto-1.1.dll` 和 `cygssl-1.1.dll`，导入库为 `libcrypto.dll.a` 和 `libssl.dll.a`。

在 Windows 上使用 MSVC 或 MingW 构建时，共享库命名为 `libcrypto-1_1.dll` 和 `libssl-1_1.dll`（32 位 Windows），`libcrypto-1_1-x64.dll` 和 `libssl-1_1-x64.dll`（64 位 x86_64 Windows），以及 `libcrypto-1_1-ia64.dll` 和 `libssl-1_1-ia64.dll`（IA64 Windows）。使用 MSVC 时，导入库命名为 `libcrypto.lib` 和 `libssl.lib`，而使用 MingW 时，导入库命名为 `libcrypto.dll.a` 和 `libssl.dll.a`。

在 VMS 上，共享图像（VMS 术语中的共享库）命名为 `ossl$libcrypto0101_shr.exe` 和 `ossl$libssl0101_shr.exe`。但是，当 OpenSSL 专门为 32 位指针构建时，共享图像命名为 `ossl$libcrypto0101_shr32.exe` 和 `ossl$libssl0101_shr32.exe`，而当为 64 位指针构建时，它们命名为 `ossl$libcrypto0101_shr64.exe` 和 `ossl$libssl0101_shr64.exe`。

#### 关于随机数生成的说明

加密安全的随机数生成对于密钥生成是必需的。OpenSSL 提供了几种选项来为内部 CSPRNG 播种。如果未正确播种，内部 CSPRNG 将拒绝提供随机字节，并出现“PRNG 未播种错误”。

可以使用 `--with-rand-seed` 选项配置播种方法，该选项可用于指定逗号分隔的播种方法列表。但是，在大多数情况下，OpenSSL 会选择合适的默认方法，因此不需要显式提供此选项。请注意，并非所有方法在所有平台上都可用。FIPS 提供程序将静默忽略未验证的种子源。

I) 在提供合适随机源的操作系统上（以系统调用或系统设备的形式），OpenSSL 将使用最佳可用方法从操作系统的随机源为 CSPRNG 播种。这对应于选项 `--with-rand-seed=os`。

II) 在没有此类合适随机源的系统上，自动播种和重新播种被禁用（`--with-rand-seed=none`），可能需要安装额外的支持软件以获取随机种子并手动重新播种 CSPRNG。请查看 `RAND_add()`、`RAND_bytes()`、`RAND_egd()` 的手册页以及 FAQ 以获取更多信息。

#### 关于汇编器模块编译的说明

某些代码路径的汇编器模块编译可能取决于当前汇编器版本是否支持某些 ISA 扩展。使用 AES-NI、PCLMULQDQ、SSSE3 和 SHA 扩展的代码路径始终会被汇编。除此之外，汇编器版本的最低要求如下表所示：

| ISA 扩展   | GNU as | nasm   | llvm    |
|------------|--------|--------|---------|
| AVX        | 2.19   | 2.09   | 3.0     |
| AVX2       | 2.22   | 2.10   | 3.1     |
| ADCX/ADOX  | 2.23   | 2.10   | 3.3     |
| AVX512     | 2.25   | 2.11.8 | 3.6 (*) |
| AVX512IFMA | 2.26   | 2.11.8 | 6.0 (*) |
| VAES       | 2.30   | 2.13.3 | 6.0 (*) |

---

(*) 尽管 AVX512 支持在 llvm 3.6 中实现，但在 7.0 之前的版本中，似乎需要显式的 `-march` 标志来编译汇编模块。然而，这会生成特定于处理器的代码，这与在运行时执行调度（通过特殊变量 `OPENSSL_ia32cap` 实现）的理念相矛盾。对于 7.0 之前的版本，可以通过强制构建过程使用以下脚本来解决问题：

    #!/bin/sh
    exec clang -no-integrated-as "$@"

在这种情况下，使用的 clang 版本并不重要，因为将检查 GNU 汇编器的版本。

---

<!-- Links  -->

[openssl-users]:<https://mta.openssl.org/mailman/listinfo/openssl-users>

[SUPPORT]:
./SUPPORT.md

[GitHub Issues]:<https://github.com/openssl/openssl/issues>

[raise an issue]:<https://github.com/openssl/openssl/issues/new/choose>

[10-main.conf]:
https://github.com/openssl/openssl/blob/master/Configurations/10-main.conf

[CMVP]:<https://csrc.nist.gov/projects/cryptographic-module-validation-program>

[ESV]:<https://csrc.nist.gov/Projects/cryptographic-module-validation-program/entropy-validations>

[SP 800-90B]:<https://csrc.nist.gov/pubs/sp/800/90/b/final>

[jitterentropy-library]:<https://github.com/smuellerDD/jitterentropy-library>