OpenSSL 用户支持资源
====================

有关如何获取商业技术支持的详细信息，请参阅 <https://www.openssl.org/support/contracts.html>。

如果您对使用 OpenSSL 有一般性问题
---------------------------------

在这种情况下，[openssl-users] 邮件列表是您应该去的地方。该列表不仅受到 OpenSSL 团队成员的关注，还受到许多其他 OpenSSL 用户的关注。在这里，您很可能会得到您的问题的答案。有关 [邮件列表](#邮件列表) 的概述，请参见下文。

如果您认为您发现了一个 Bug
----------------------------

*注意：本节假设您想要报告它或弄清楚并修复它。这里写的内容不应被视为如何获得一个正常运行的生产安装的指南*

如果您在使用 OpenSSL 时遇到任何问题，请首先采取以下步骤：

- 在邮件列表和/或 GitHub 问题中搜索，看看问题是否已经报告过。
- 从仓库下载最新版本，看看问题是否已经解决。
- 配置时不使用汇编支持（`no-asm`），检查问题是否仍然存在。
- 移除编译器优化标志。

请记住：仅仅因为某些功能没有按照您的预期工作，并不一定意味着它是 OpenSSL 中的一个 bug。如果您不确定，请考虑搜索邮件存档并向 [openssl-users] 邮件列表发帖提问。

### 提交问题

如果您希望报告一个 bug，请在 GitHub 上 [提交问题][github-issues]，并包含以下信息：

- OpenSSL 版本：`openssl version -a` 的输出
- 配置数据：`perl configdata.pm --dump` 的输出
- 操作系统名称、版本、硬件平台
- 编译器详情（名称、版本）
- 应用程序详情（名称、版本）
- 问题描述（已知的重现问题的步骤）
- 堆栈回溯（如果应用程序崩溃）

不仅软件中的错误，文档中的错误，特别是手册页中的错误，也可以作为问题报告。

### 提交拉取请求

最快修复 bug 的方法是自己修复它 ;-)。如果您有编程经验并知道如何修复 bug，您可以提交一个拉取请求。详细信息请参见 [贡献][contributing] 部分。

不要犹豫提交拉取请求，即使只是一个小的更改，比如文档中的语法或排版错误。

邮件列表
========

OpenSSL 维护了多个 [mailing lists]，用于各种目的。最重要的列表包括：

- [openssl-users]    用于关于使用 OpenSSL 软件的一般问题和用户之间的讨论。

- [openssl-announce] 用于向 OpenSSL 社区发布官方公告。

- [openssl-project]  用于讨论开发路线图和治理。

只有订阅者才能在 [openssl-users] 或 [openssl-project] 上发帖。然而，存档是公开的。有关更多信息，请参见 [mailing lists] 页面。

曾经有一个 [openssl-dev] 列表，但由于开发现在以 GitHub 拉取请求的形式进行，该列表已被停用。尽管不再活跃，但可搜索的存档可能仍然包含有用的信息。

<!-- 链接 -->

[mailing lists]:     https://www.openssl.org/community/mailinglists.html
[openssl-users]:     https://mta.openssl.org/mailman/listinfo/openssl-users
[openssl-announce]:  https://mta.openssl.org/mailman/listinfo/openssl-announce
[openssl-project]:   https://mta.openssl.org/mailman/listinfo/openssl-project
[openssl-dev]:       https://mta.openssl.org/mailman/listinfo/openssl-dev
[github-issues]:     https://github.com/openssl/openssl/issues/new/choose
[contributing]:      https://github.com/openssl/openssl/blob/master/CONTRIBUTING.md