Windows 平台笔记
=================

- [使用 Visual C++ 进行本地构建](#使用-visual-c++-进行本地构建)
- [使用 Embarcadero C++Builder 进行本地构建](#使用-embarcadero-c++builder-进行本地构建)
- [使用 MinGW 进行本地构建](#使用-mingw-进行本地构建)
- [链接本地应用程序](#链接本地应用程序)
- [使用 Cygwin 进行托管构建](#使用-cygwin-进行托管构建)

在 Windows 平台上构建和运行 OpenSSL 有多种选项。

“本地” OpenSSL 在运行时直接使用 Windows API。要构建本地 OpenSSL，您可以使用：

    Microsoft Visual C++ (MSVC) C compiler on the command line

或者

    Embarcadero C++Builder

或者

    MinGW cross compiler
    run on the GNU-like development environment MSYS2
    or run on Linux or Cygwin

“托管” OpenSSL 依赖于外部 POSIX 兼容层进行构建（使用 GNU/Unix shell、编译器和工具）并在运行时使用。对于此选项，您可以使用 Cygwin。

使用 Visual C++ 进行本地构建
==============================

使用 Visual C++ 进行的本地构建具有 `VC-*` 前缀。

需求详情
-------------------

除了 `INSTALL.md` 中列出的需求和说明外，还需要以下内容：

### Perl

我们推荐使用 Strawberry Perl，可从 <http://strawberryperl.com/> 获取。请阅读 NOTES.PERL 了解更多信息，包括使用 CPAN。另一种选择是
ActiveState Perl，<https://www.activestate.com/ActivePerl>，您可能需要通过 <https://platform.activestate.com/ActiveState>
显式构建 Perl 模块 Win32/Console.pm 并下载它。

### Microsoft Visual C 编译器

由于这些是专有且不断变化的，我们无法测试所有版本。较旧的版本可能无法工作。尽可能使用最新版本。

### Netwide Assembler (NASM)

NASM 是唯一支持的汇编器。可从 <https://www.nasm.us> 获取。

快速开始
-----------

1. 安装 Perl

2. 安装 NASM

3. 确保 Perl 和 NASM 都在您的 %PATH% 中

4. 使用具有管理员权限的 Visual Studio 开发者命令提示符，根据目标架构选择其变体。或者运行 `cmd` 并执行 `vcvarsall.bat`，选项包括
   `x86`、`x86_amd64`、`x86_arm`、`x86_arm64`、`amd64`、`amd64_x86`、`amd64_arm` 或 `amd64_arm64`。这将设置 `nmake.exe`、
   `cl.exe` 等所需的环境变量。请参阅 <https://docs.microsoft.com/cpp/build/building-on-the-command-line>

5. 从 OpenSSL 源代码目录的根目录输入
    - `perl Configure VC-WIN32`     如果您想要 32 位 OpenSSL 或
    - `perl Configure VC-WIN64A`    如果您想要 64 位 OpenSSL 或
    - `perl Configure VC-WIN64-ARM` 如果您想要 Windows on Arm (win-arm64) OpenSSL 或
    - `perl Configure VC-WIN64-CLANGASM-ARM` 如果您想要 Windows on Arm (win-arm64) OpenSSL 并使用 clang-cl 作为汇编器或
    - `perl Configure VC-CLANG-WIN64-CLANGASM-ARM` 如果您想要 Windows on Arm (win-arm64) OpenSSL 并使用 clang-cl 作为编译器和汇编器或
    - `perl Configure VC-WIN32-HYBRIDCRT` 如果您想要 32 位 OpenSSL 依赖于 Universal CRT 或
    - `perl Configure VC-WIN64A-HYBRIDCRT` 如果您想要 64 位 OpenSSL 依赖于 Universal CRT 或
    - `perl Configure`              让 Configure 自行确定平台

6. `nmake`

7. `nmake test`

8. `nmake install`

有关完整的安装说明，或在任何阶段出现问题时，请查看 INSTALL.md 文件。

安装目录
------------------------

在大多数 Unix 平台上，安装目录在构建时通过常量定义确定。然而，在 Windows 平台上，安装目录通过注册表键确定，因为通常会构建 OpenSSL 并将其安装到各种位置。

以下键：

    `\\HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\OpenSSL-<version>-<ctx>\OPENSSLDIR`
    `\\HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\OpenSSL-<version>-<ctx>\ENGINESDIR`
    `\\HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\OpenSSL-<version>-<ctx>\MODULESDIR`

可以由管理员设置，openssl 将从中获取的路径作为 OPENSSLDIR、ENGINESDIR 和 MODULESDIR 的值。

要启用从 Windows 构建中读取注册表键，请在 Configure 命令行中添加 `-DOPENSSL_WINCTX=<string>`。此定义用于在构建时构造库构建特定的注册表键路径，格式为：
`\\HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432node\OpenSSL-<version>-<ctx>`

其中 `<version>` 是正在构建的库的主版本和次版本，`<ctx>` 是 `-DOPENSSL_WINCTX` 指定的值。这允许在单个系统上创建和安装多个 openssl 构建，每个库可以使用自己的一组注册表键。

请注意，可从 <https://github.com/openssl/installer> 获取的安装程序将在运行时设置这些键。

Windows 平台行为的摘要表

| `OSSL_WINCTX` | 注册表键 | OpenSSL 行为            |
|---------------|------|-----------------------|
| 已定义           | 已定义  | OpenSSL 从注册表读取路径      |
| 已定义           | 未定义  | OpenSSL 在模块/配置加载时返回错误 |
| 未定义           | N/A  | OpenSSL 使用构建时默认值      |

Universal Windows Platform 构建的特别说明，即 `VC-*-UWP`
-------------------------------------------------------------------

- UWP 目标仅支持构建静态和动态库。

- 您应该将平台类型定义为 `uwp`，并通过 `vcvarsall.bat` 定义目标架构，然后再编译。例如，如果您想要构建 `arm64` 构建，您应该运行
  `vcvarsall.bat x86_arm64 uwp`。

使用 Embarcadero C++Builder 进行本地构建
=========================================

此工具链（Turbo/Borland C++ 的后代）是 MSVC 的替代方案。OpenSSL 目前包含一个实验性的 32 位配置，面向 v10.3.3 社区版的 Clang
编译器 (`bcc32c.exe`)。<https://www.embarcadero.com/products/cbuilder/starter>

1. 安装 Perl。

2. 打开 RAD Studio 命令提示符。

3. 转到 OpenSSL 源代码目录的根目录并运行：
   `perl Configure BC-32 --prefix=%CD%`

4. `make -N`

5. `make -N test`

6. 使用此 OpenSSL 构建您的程序：
    * 将您的包含搜索路径设置为 OpenSSL 的 "include" 子目录。
    * 将您的库搜索路径设置为 OpenSSL 源代码目录。

请注意，这是一个非常实验性的配置。64 位和其他 Configure 选项的支持仍在进行中。

使用 MinGW 进行本地构建
=========================

MinGW 提供了另一种通过交叉编译构建本地 OpenSSL 的方式。

* 通常在 Windows 上的 GNU 风格环境 MSYS2 中进行构建。

  MSYS2 提供了 GNU 工具、类 Unix 命令提示符和应用程序的 UNIX 兼容层。然而，在这种情况下，它仅用于构建 OpenSSL。生成的 OpenSSL 不依赖于 MSYS2 运行，是完全本地的。

  需求详情

    - MSYS2 shell，来自 <https://www.msys2.org/>

    - Perl，至少版本 5.10.0，通常随 MSYS2 预安装

    - make，使用 `pacman -S make` 安装到 MSYS2 环境中

    - MinGW[64] 编译器：`mingw-w64-i686-gcc` 和/或 `mingw-w64-x86_64-gcc`。这些编译器必须在您的 MSYS2 $PATH 中。常见错误是没有将这些编译器放在 $PATH 中。MSYS2 版本的 gcc 在此处无法正常工作。

  在 MSYS2 shell 中，根据目标架构进行配置：

      ./Configure mingw ...

  或

      ./Configure mingw64 ...

  或

      ./Configure ...

  对于默认架构。

  除此之外，请遵循 `INSTALL.md` 中的 Unix / Linux 说明。

* 也可以在 Linux 或 Cygwin 上构建 mingw[64]。

  在这种情况下，使用相应的 `--cross-compile-prefix=` 选项进行配置。例如

      ./Configure mingw --cross-compile-prefix=i686-w64-mingw32- ...

  或

      ./Configure mingw64 --cross-compile-prefix=x86_64-w64-mingw32- ...

  这要求您已安装必要的 mingw[64] 交叉编译附加包。

链接本地应用程序
===========================

本节适用于所有本地构建。

如果您链接到静态 OpenSSL 库，则需要额外链接到 `WS2_32.LIB`、`GDI32.LIB`、`ADVAPI32.LIB`、`CRYPT32.LIB` 和 `USER32.LIB`
。那些开发非交互式服务应用程序的人可能会对链接到 `GDI32.LIB` 和 `USER32.LIB`
感到担忧，因为它们与交互式桌面相关联，而服务进程无法访问。该工具包旨在检测当前执行的上下文（GUI、控制台应用程序或服务）并相应地采取行动，即是否实际进行
GUI 调用。此外，那些希望 `/DELAYLOAD:GDI32.DLL` 和 `/DELAYLOAD:USER32.DLL` 并实际将它们排除在服务进程之外的人应考虑实现并从
.exe 映像中导出自己的 `_OPENSSL_isservice`，而不依赖于 `USER32.DLL`。例如，在 Windows Vista 及更高版本上，您可以：

       __declspec(dllexport) __cdecl BOOL _OPENSSL_isservice(void)
       {
           DWORD sess;

           if (ProcessIdToSessionId(GetCurrentProcessId(), &sess))
               return sess == 0;
           return FALSE;
       }

如果您链接到 OpenSSL .DLL，则需要在应用程序代码中包含一个小型的“shim”代码片段，该片段提供了 OpenSSL BIO 层与编译器运行时之间的粘合剂。请参阅 OPENSSL_Applink 手册页。

使用 Cygwin 进行托管构建
==========================

Cygwin 在 Windows 子系统之上实现了一个 POSIX/Unix 运行时系统 (`cygwin1.dll`)，并提供了一个 Bash shell 和 GNU 工具环境。因此，使用 Cygwin 构建 OpenSSL 的过程与 Unix 过程几乎相同。

要使用 Cygwin 构建 OpenSSL，您需要：

* 安装 Cygwin，参见 <https://cygwin.com/>

* 安装 Cygwin Perl，至少版本 5.10.0，并确保它在 $PATH 中

* 运行 Cygwin Bash shell

除此之外，请遵循 `INSTALL.md` 中的 Unix / Linux 说明。

注意：`make test` 和正常文件操作可能会在作为文本挂载的目录中失败（即 `mount -t c:\somewhere /home`），因为 Cygwin 会剥离回车符。为避免这种情况，请确保使用二进制挂载，例如 `mount -b c:\somewhere /home`。