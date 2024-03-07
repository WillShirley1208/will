---
title: python pydantic
date: 2024-03-05 11:44:46
tags: pydantic
categories: python
---



## model_validator

> `model_validator` 是 Pydantic 库提供的一个装饰器，用于定义模型验证器函数。模型验证器函数是一种特殊的方法，用于在创建模型实例或更新模型属性时执行自定义的验证逻辑。

```python
@model_validator(mode="after")
def validate_engine_and_run_func(self):
    if self.search_engine is None:
        self.search_engine = SearchEngine.from_search_config(self.config.search, proxy=self.config.proxy)
    return self
```

