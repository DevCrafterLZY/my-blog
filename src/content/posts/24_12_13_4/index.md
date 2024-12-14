---
title: 配置Sphinx
published: 2024-12-14
description: "配置Sphinx"
tags: ["Sphinx"]
category: Notes
draft: false
---

## sphinx

sphinx是一个python库，其功能是可以通过项目的代码文件生成rst文件(类似于md文件)，即可以为项目自动生成文档

### 1. 安装

在python环境中，使用pip安装sphinx。

```bash
pip install sphinx
```

### 2. 运行
新建一个项目，在项目文件夹下，运行以下命令。

```bash
sphinx-quickstart
```
回答下面的问题：

除了`Separate source and build directories (y/n) [n]: y`其他可以随便写后面都可以在conf.py中修改：
```
> Separate source and build directories (y/n) [n]: y
> Project name: test
> Author name(s): test
> Project release []: 1.0
```
### 3. 使用
sphinx库的功能可以拆分为两个部分，一个是通过你的项目文件，生成一系列的rst文件，另一个是将rst文件生成html文件。

并且两部分相互独立，也就是可以随意修改生成的rst文件，然后再生成html。

#### 3.1 生成rst文件
假设有如下的项目文件夹结构：

```
test
├── build   // 生成的html文件
├── source  // 生成的rst文件
│   ├── conf.py
│   └── index.rst
└── src     // 源代码文件
    └── test.py
```

`conf.py`文件如下, 请注意有注释的部分：
```python
import os
import sys

sys.path.insert(0, os.path.abspath('../src'))   # 改成你的源代码文件夹

project = '项目名称'
copyright = ('自行修改')
author = ('自行修改')


extensions = [
    'sphinx.ext.todo',
    'sphinx.ext.viewcode',
    'sphinx.ext.autodoc',
    'sphinx.ext.autosummary',
]

templates_path = ['_templates']
exclude_patterns = []

# -- Options for HTML output -------------------------------------------------

html_theme = "sphinx_rtd_theme"  # 使用的主题可以更换
html_static_path = ['_static']
html_domain_indices = False
html_show_sourcelink = False
add_module_names = False
# sphinx生成时需要当前环境中有项目所有的依赖包
# 如果你的项目中有一些依赖包是没有安装的，可以在这里添加忽略就可以正常生成文档
autodoc_mock_imports = [

]

```

以下命令均在test目录下运行。

```bash
sphinx-apidoc -o ./source ./src/
```
可以看到在source文件夹下生成了一系列的rst文件。

#### 3.2 生成html文件
根据你的系统运行make文件。
```bash
./make html
```
然后在build文件夹下就可以看到生成的html文件。

点击index.html就可以看到文档了。

:::tip
sphinx更多的使用方法和rst的语法可以参考 https://zh-sphinx-doc.readthedocs.io/en/latest/contents.html
:::