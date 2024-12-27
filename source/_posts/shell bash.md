---
title: shell bash
date: 2024-07-30 18:39:34
tags: bash
categories: shell
---

- the shell assigns its **exit code** to the `$?` environment variable. The `$?` variable is how we usually test whether a script has succeeded or not in its execution.

# syntax

## set -x

> ​	1.	**Debugging**: When you’re trying to troubleshoot a script, set -x helps you see exactly what commands are being executed and in what order.
>
> ​	2.	**Selective Debugging**: You can use set -x and set +x around specific parts of your script to narrow down the problematic sections.

```shell
#!/bin/bash

echo "This is a normal message"

set -x  # Start debugging from this point
name="John"
echo "Hello, $name"

set +x  # Stop debugging after this point
echo "Debugging mode is now off"
```

output:

```shell
This is a normal message
+ name=John
+ echo 'Hello, John'
Hello, John
Debugging mode is now off
```

## shift 2

- Removes the first two positional parameters from command line arguments
- Shifts remaining parameters left by 2 positions
- Useful when processing command line options/arguments in loops



# point

- `(cd third-party && ...)` 这样的语法，这是一个命令替换，它实质上是先临时改变到 `third-party` 目录执行里面的命令，然后脚本会继续在原先的目录中执行。