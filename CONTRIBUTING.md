# 如何为 OpenSSL 贡献代码

请访问我们的[入门指南]页面，了解更多关于如何贡献的想法。

[入门指南]: <https://openssl-library.org/community/getting-started>

开发工作在 GitHub 上的 [openssl/openssl] 仓库中进行。

[openssl/openssl]: <https://github.com/openssl/openssl>

要请求新功能、提问或报告错误，请在 GitHub 上[提交问题](https://github.com/openssl/openssl/issues)。

要提交补丁或实现新功能，请在 GitHub 上[提交拉取请求](https://github.com/openssl/openssl/pulls)。如果你计划进行大规模贡献，请在开始工作之前提交问题，以获得社区的反馈。可能已经有人在处理相同的事情，或者可能有特殊的原因导致某个功能未被实现。

为了便于审查和接受你的拉取请求，请遵循以下指南：

1. 任何非微小的贡献都需要[贡献者许可协议] (CLA)，以授予我们使用你的代码的权限。如果你的贡献太小而不需要 CLA（例如，修复拼写错误），请在提交信息的末尾单独一行添加文本 "`CLA: trivial`"，并用空行分隔，如下所示：

   ```
       微小更改的单行摘要

       可选的提交信息主体。可能包含一两句话解释微小更改。

       CLA: trivial
   ```

   仅在 GitHub 拉取请求描述中添加 "`CLA: trivial`" 是不够的。

   [贡献者许可协议]: <https://www.openssl.org/policies/cla.html>

   如果在提交后发现缺少 "`CLA: trivial`" 行，请执行以下操作：

   ```
       git commit --amend
       # 添加该行，保存并退出编辑器
       git push -f [<仓库> [<分支>]]
   ```

2. 所有源文件的开头应包含以下文本（每行开头应有适当的注释字符，并更新年份）：

   ```
       版权所有 20xx-20yy The OpenSSL Project Authors。保留所有权利。

       根据 Apache License 2.0（“许可证”）授权。除非遵守许可证，否则不得使用此文件。你可以在源代码分发中的 LICENSE 文件或 https://www.openssl.org/source/license.html 获取许可证副本。
   ```

3. 补丁应尽可能保持最新；预计需要经常重新整理（rebase）。我们不接受合并提交，你需要在提交前删除它们（通常通过重新整理）。

4. 提供的代码应遵循我们的[编码风格]和[文档策略]，并且编译时不应有警告。有一个[Perl 工具](https://github.com/openssl/openssl/blob/master/util/check-format.pl)可以帮助查找代码格式错误和其他编码风格问题。在可用 `gcc` 或 `clang` 的情况下，应使用 `--strict-warnings` 的 `Configure` 选项。OpenSSL 在许多不同的平台上编译：请确保你只使用可移植的功能。通过 GitHub Actions 的干净构建是必需的。每当创建或更新 PR 时，提交者会自动启动这些构建。

   [编码风格]: https://openssl-library.org/policies/technical/coding-style/
   [文档策略]: https://openssl-library.org/policies/technical/documentation-policy/

5. 尽可能地，代码贡献应包括测试。这些测试可以添加到现有测试中，或完全新建。请参阅 [test/README.md](https://github.com/openssl/openssl/blob/master/test/README.md) 了解测试框架的信息。

6. 新功能或更改的功能必须包含文档。请查看 `doc/man[1357]` 中的 `.pod` 文件，了解我们的文档风格示例。运行 `make doc-nits` 以确保你的文档更改是干净的。

7. 对于用户可见的更改（API更改、行为更改等），请考虑在[CHANGES.md](CHANGES.md)文件中添加一条备注。这可能是一个对更改的概括性描述，并可以解释更详细的内容。可以参考现有的条目来获取灵感。请注意，这不仅仅是git日志中的一行摘要。同时请注意，安全修复也会在[CHANGES.md](CHANGES.md)文件中有一条记录。这个文件帮助用户在不查阅git日志中较高噪音比的内容的情况下，获取特定版本的深入信息。

8. 有关如何集成新加密库模块的错误输出的指南，请参阅 [crypto/err/README.md](https://github.com/openssl/openssl/blob/master/crypto/err/README.md)。