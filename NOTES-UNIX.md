# UNIX 平台笔记

## 适用于 Windows 的 Unix/POSIX 运行时系统

请参阅 [Windows 平台笔记](NOTES-WINDOWS.md)。

## OpenSSL 使用编译器链接程序和共享库

OpenSSL 生成的 Makefile 使用 C 编译器命令行来链接程序、共享库和动态加载的共享对象。因此，任何传递给配置脚本的链接选项**必须**是编译器可以接受的格式。不同系统之间有所不同，有些系统编译器直接接受链接器标志，而其他系统则需要以 `-Wl,` 的形式传递。你需要阅读编译器文档以确定哪些格式是可接受的，并查阅 `ld(1)` 以了解可用的链接器选项。

## 共享库和非默认安装位置

每个 Unix 系统都有自己的默认共享库位置，例如 `/lib`、`/usr/lib` 或 `/usr/local/lib`。如果库安装在非默认位置，动态链接的二进制文件将无法找到它们，因此无法运行，除非通过定义运行时共享库搜索路径来提供一些帮助。

对于 OpenSSL 的应用程序（`openssl` 命令），我们的配置脚本通常**不会**为你设置运行时共享库搜索路径。因此，建议在配置时显式设置它，除非库安装在你知道在默认列表中的目录中。

运行时共享库搜索路径的指定方式因操作系统和版本而异，并且在各自的文档中有不同的描述；最常见的是 RPATH 的变体（注意：ELF 系统有两个这样的标签，详见下文）。

设置运行时共享库搜索路径的可能选项包括以下内容：

```
-Wl,-rpath,/whatever/path   # Linux, *BSD 等
-R /whatever/path           # Solaris
-Wl,-R,/whatever/path       # AIX (-bsvr4 在内部传递)
-Wl,+b,/whatever/path       # HP-UX
-rpath /whatever/path       # Tru64, IRIX
```

OpenSSL 的配置脚本识别所有这些选项，并将它们传递给生成的 Makefile。（实际上，所有以 `-Wl,` 开头的参数都被识别为链接器选项。）请注意，`-Wl` 中的 `l` 是小写的 L，而不是数字 1。

请不要在运行时共享库搜索路径中使用逐字目录！一些 OpenSSL 配置目标为多库安装添加了额外的目录级别。为了帮助解决这个问题，生成的 Makefile 包含了一个名为 LIBRPATH 的变量，该变量可以与运行时共享库搜索路径选项一起使用，如下例所示：

```
$ ./Configure --prefix=/usr/local/ssl --openssldir=/usr/local/ssl \
    '-Wl,-rpath,$(LIBRPATH)'
```

在现代 ELF 系统上，有两个运行时搜索路径标签需要考虑，`DT_RPATH` 和 `DT_RUNPATH`。共享对象的搜索顺序如下：

1. 使用 `DT_RPATH` 中指定的目录，除非也设置了 `DT_RUNPATH`。
2. 使用环境变量 `LD_LIBRARY_PATH`。
3. 使用 `DT_RUNPATH` 中指定的目录。
4. 使用系统共享对象缓存和默认目录。

这意味着，如果库在 `DT_RPATH` 指定的路径中找到（并且未设置 `DT_RUNPATH`），环境变量 `LD_LIBRARY_PATH` 中的值将无关紧要。

`DT_RPATH` 或 `DT_RUNPATH` 中哪一个默认设置似乎取决于系统。例如，根据文档，`DT_RPATH` 在 Solaris 上似乎已被弃用，转而使用 `DT_RUNPATH`，而在 Debian GNU/Linux 上，两者都可以设置，并且在撰写本文时，`DT_RPATH` 是默认值。

选择设置哪个运行时搜索路径标签取决于你的系统，请参阅 `ld(1)` 以获取你系统的具体信息。例如，在 Debian GNU/Linux 系统上确保设置 `DT_RUNPATH` 而不是 `DT_RPATH` 的方法是告诉链接器设置新的 dtags，如下所示：

```
$ ./Configure --prefix=/usr/local/ssl --openssldir=/usr/local/ssl \
    '-Wl,--enable-new-dtags,-rpath,$(LIBRPATH)'
```

值得一提的是，一些/大多数 ELF 系统通过解释 `$ORIGIN` 和其他一些内部变量来实现对相对于当前可执行文件目录的运行时搜索路径的支持。请查阅系统文档。

## 链接你的应用程序

与 OpenSSL（或其他任何）共享库动态链接的第三方应用程序在非默认位置面临完全相同的问题。上述 OpenSSL 配置选项可能对目标应用程序的链接有影响，也可能没有影响。“可能”意味着在某些情况下，只需使用 `-L/whatever/path -lssl -lcrypto` 自然地链接 OpenSSL 共享库就足够了。但也有一些情况下，你必须在链接应用程序时显式指定运行时搜索路径。请查阅系统文档并参考上述部分作为灵感...

共享 OpenSSL 构建还会安装静态库。链接后者可能需要特别注意，因为链接器通常首先查找共享库，并且往往对静态 OpenSSL 库“视而不见”。参考系统文档就足够了，如果没有一个特殊情况。在 AIX 上，静态库（在共享构建中）的名称不同，添加 `_a` 后缀以链接它们，例如 `-lcrypto_a`。