---
title: bazel
date: 2024-11-25 11:42:10
tags: tool
categories: build
---

**MODULE.bazel**

- The `MODULE.bazel` file, which identifies the directory and its contents as a Bazel workspace and lives at the root of the project's directory structure. It's also where you specify your external dependencies.

**BUILD**

- One or more [`BUILD` files](https://bazel.build/reference/glossary#build-file), which tell Bazel how to build different parts of the project. A directory within the workspace that contains a `BUILD` file is a package. (More on packages later in this tutorial.)

