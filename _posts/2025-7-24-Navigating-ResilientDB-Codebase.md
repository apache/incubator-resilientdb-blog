---
layout: article
title: "Navigating ResilientDB's C++ Codebase: Empowering Contributors with Modern Tooling"
author: Harish Krishnakumar
tags: IDE Intellisense, LSP, Bazel Tooling, Hedronvision ,Clang
aside:
    toc: true
article_header:
  type: overlay
  theme: dark
  background_color: '#000000'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(0, 204, 154 , .2), rgba(51, 154, 154, .2))'
    opacity: 0.2
    src: /assets/images/memlens/profiling.jpg

---

## Navigating ResilientDB's C++ Codebase: Empowering Contributors with Modern Tooling

ResilientDB is a high-performance blockchain platform written in modern C++17. If you’ve worked with C++ before, you know that managing large codebases can be challenging—not just because of the language’s complexity, but also due to the tools used to build and organize the project. In the C++ world, build systems play a crucial role: they take care of compiling source files, managing dependencies, and producing the final executable. While CMake is a popular choice for many open-source projects, ResilientDB uses Bazel—a powerful build tool developed at Google that excels at handling complex, multi-language codebases with many modules and dependencies.

Understanding how a build system works is essential for any C++ developer. A build system automates the process of transforming source code into binaries, managing everything from compilation flags to linking libraries. This is especially important in large projects, where manual compilation would be error-prone and time-consuming. CMake is widely used for this purpose, but Bazel offers advanced features for scaling to very large codebases and supporting multiple languages.

Modern development environments (IDEs) offer features like Intellisense, autocomplete, and code navigation, which are powered by language servers and configuration files that describe how the code is built. These features are not just conveniences—they are essential for stepping through code, debugging, and understanding how different parts of a project fit together. They make it much easier to dive into the details of an implementation, accelerate feature development, and lower the barrier for new contributors.

However, Bazel doesn’t natively generate the configuration files (like `compile_commands.json`) that many IDEs and language servers rely on for these features. This can make it difficult to get full code intelligence and navigation in editors like VSCode or CLion when working with Bazel-based projects.

To solve this, we integrated a tool called [Hedronvision's Bazel Compile Commands Extractor](https://github.com/hedronvision/bazel-compile-commands-extractor). This tool generates a `compile_commands.json` file based on the current state of your Bazel build. This file is often called a *Compilation Database* and helps tools to generate intellisense. With this file in place, you can use a Language Server Protocol (LSP) extension in your editor to unlock powerful, interactive code exploration—making it easy to step through functions, debug, and contribute to the core engine.

In this post, I'll show you how to set up your environment so you can:
- Effortlessly navigate the ResilientDB codebase
- Unlock powerful autocomplete and code exploration features
- Lower the barrier for new contributors to jump into C++ development

Let's make contributing to ResilientDB accessible to everyone—because the more eyes and ideas we have, the stronger the project becomes.

## Setting up Intellisense for ResilientDB's C++ Projects

### Setting up ResilientDB Core

Follow the steps in the [Build and Deploy ResilientDB](https://github.com/apache/incubator-resilientdb?tab=readme-ov-file#build-and-deploy-resilientdb) to setup the resilientdb project.

#### 1. Generate compile_commands.json with Hedronvision

Once your ResilientDB project is set up, you can generate the `compile_commands.json` file by running the following command from your project root:

```sh
bazel run @hedron_compile_commands//:refresh_all
```

This will create or update the `compile_commands.json` file, which is essential for enabling full-featured code navigation and autocomplete in your IDE.

#### 2. Install clangd (C++ Language Server)

If you're using VS Code, simply install the [clangd extension](https://marketplace.visualstudio.com/items?itemName=llvm-vs-code-extensions.vscode-clangd) from the Extensions marketplace. VS Code will handle the installation of clangd for you—no manual setup required.

#### 3. Configure your editor to use clangd

Most modern editors (like VSCode, Neovim, or CLion) support clangd via extensions or built-in integration. For VSCode:

- Install the [clangd extension](https://marketplace.visualstudio.com/items?itemName=llvm-vs-code-extensions.vscode-clangd).
- Open your ResilientDB project folder in VSCode.
- The extension should automatically detect your `compile_commands.json` file and enable code navigation, autocomplete, and other features.
- You will see a progress bar at the bottom of your screen displaying the progress of indexing. This step is essentially the LSP extension trying to crawl through your code and configuring the autocomplete.

#### 4. Handling Missing Autocomplete for Some Modules

While the steps above will enable autocomplete and code navigation for most of the codebase, you may occasionally notice that some modules or files are missing autocomplete or show errors. This usually happens when certain parts of the project haven't been built yet, so their compilation information isn't included in the `compile_commands.json` file.

To resolve this:
- Identify the module or target that's missing autocomplete (for example, a storage engine or a smart contract library).
- Navigate to the folder containing the relevant `BUILD` file.
- Build the target using Bazel:

```sh
bazel build //path/to/your/target
```

For example, to build the storage engine module:

```sh
bazel build chain/storage/leveldb
```

After building the target, restart the clangd extension in your editor. This will prompt clangd to re-index the codebase, and autocomplete should now work for the newly built modules.

Now you’re ready to explore, debug, and contribute to ResilientDB with full code intelligence support!

### Setting up ResilientDB GraphQL Proxy Server

The same steps can be used for generating a *Compilation Database*. Follow the instructions in the [Build and Deploy GraphQL Proxy](https://github.com/apache/incubator-resilientdb-graphql/blob/main/README.md). Once the bazel build process is completed, use this command to generate the `compile_commands.json` file and start hacking.

```sh
bazel run @hedron_compile_commands//:refresh_all
```

Since the Proxy Server contains code written in both C++ and Python, we recommend installing the following VS Code extensions for full Intellisense support:

- [clangd](https://marketplace.visualstudio.com/items?itemName=llvm-vs-code-extensions.vscode-clangd) — for C++ code navigation and autocomplete
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) — for Python code navigation and autocomplete

### How do we use these tools internally?

At ResilientDB, we rely heavily on Intellisense and advanced code navigation tools to work efficiently within our large and evolving codebase. By leveraging AI-powered editors like Cursor and GitHub Copilot, our team can quickly understand unfamiliar parts of the project, receive intelligent code suggestions, and even generate boilerplate or complex code snippets on the fly. This is especially valuable as we work on ambitious projects, such as developing a new storage engine and exploring ways to make our architecture pluggable for different storage backends to suit various use cases. Intellisense, combined with robust debugging and benchmarking workflows, enables us to iterate rapidly, catch issues early, and keep a sharp focus on both performance and developer productivity as we expand the capabilities of ResilientDB.

### Debugging, Profiling, and Observability

Stay tuned! Next week, we’ll be adding a detailed section on how to enable debugging and profiling flags in Bazel, as well as how to use ResLens’s flamegraph feature for observability and performance analysis. This will include practical steps, example commands, and tips for getting the most out of these powerful tools.

