---
title: python pytest
date: 2024-05-20 15:21:43
tags: pytest
categories: python
---

## test

- 查看测试详情

  ```shell
  pytest -v
  ```

  
  
- 测试信息打印

  > 默认情况下，pytest 会捕获所有的输出。这意味着在测试运行期间， `print` 语句的输出不会显示在控制台上。
  >
  > 如果想看到 `print` 语句的输出，需要使用 `-s` 选项来告诉 pytest 不要捕获输出

  ```shell
  pytest -s test_web_base.py
  ```

  

- 测试指定方法

  ```python
  pytest tests/unit_tests/document_loaders/test_web_base.py::类名::方法名
  ```

  

- 测试被标记的方法

  ```python
  @pytest.mark.finished
  def test_func1():
      assert 1 == 1
  ```

  ```shell
  pytest -m finished tests/test-function/test_with_mark.py
  ```

  

- 生成测试报告

  ```shell
  pytest --html=report.html
  ```

  