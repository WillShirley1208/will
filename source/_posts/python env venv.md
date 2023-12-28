---
title: python venv
date: 2023-11-21 11:21:09
tags: venv
categories: python
---

## 虚拟环境

在 Linux 中配置 Python 项目的运行虚拟环境通常使用 `venv`（虚拟环境）模块。以下是一般的步骤：

1. **安装 venv 模块（如果尚未安装）：**

   检查你的 Python 版本并确保安装了 `venv` 模块。

2. **创建虚拟环境：**

   在项目目录中，运行以下命令创建一个名为 `myenv` 的虚拟环境：

   ```
   python3 -m venv myenv
   ```

   这将在当前目录创建一个名为 `myenv` 的虚拟环境文件夹。

3. **激活虚拟环境：**

   激活虚拟环境可通过以下命令：

   ```
   source myenv/bin/activate
   ```

   这将激活名为 `myenv` 的虚拟环境。激活后，你的终端提示符可能会显示虚拟环境的名称。

4. **安装项目依赖：**

   在激活的虚拟环境中，使用 `pip` 安装项目所需的依赖：

   ```
   pip install -r requirements.txt
   ```

   其中 `requirements.txt` 是包含项目依赖项的文件。

5. **退出虚拟环境：**

   当你完成项目工作后，可以通过以下方式退出虚拟环境：

   ```
   deactivate
   ```

   这将使虚拟环境退出，并恢复到系统默认环境。

使用虚拟环境有助于隔离项目的依赖，避免与系统其他项目的依赖冲突，并提供更干净的开发环境。



### uvicorn

> 正常情况下虚拟环境 /path/to/venv/bin/ 下面是有 uvicorn 的启动命令的，即 /xxx/venv/bin/uvicorn

#### 如果当前虚拟环境不识别`uvicorn`，可通过

- 方式一

```shell
python -m uvicorn xxx:app --reload
```

- 方式二

```
pip install --force-reinstall uvicorn
```

