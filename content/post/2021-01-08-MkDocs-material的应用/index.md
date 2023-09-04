---
title:      MkDocs-material的应用
description:   MkDocs的第三方主题——material
date:       2021-01-08
image: post-bg-210108.png
categories: 
    - github projects
tags:
    
---

## 效果

![image-20210108155956618](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20210108155956618.png)

[https://TetsuOu.github.io/Bookmarks/](https://TetsuOu.github.io/Bookmarks/)

## 官方地址

[https://squidfunk.github.io/mkdocs-material/](https://squidfunk.github.io/mkdocs-material/)

以下只做一下简单介绍，更多信息参考官方介绍。

## 开始

MkDocs-material是MkDocs的一个第三方主题，界面优美。使用前应注意MkDocs是必需的。



### 安装

可利用pip进行安装，除此之外，还可以使用docker、git进行安装，详见官网。

```bash
pip install mkdocs-material
```

### 使用

在项目中的配置文件`mkdocs.yml`中进行配置：

```yaml
theme: 
    name: material
```

#### 更改网站图标

新的图标放在`docs`文件夹内的指定路径，如`img/favicon.ico`

```yaml
theme: 
    favicon: img/favicon.ico
```

#### 更改页面布局

参数配置如下，可以使最顶层的选项排序到上方

```yaml
theme: 
    features:
        - navigation.tabs
```

如：

![image-20210108160950107](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20210108160950107.png)

```yaml
nav:
    - Introduction: 'index.md'
    - SiteNavigation: 'https://TetsuOu.github.io/SiteNavigation/'
    - Program:  
        - Android: 'Program/Android.md'
        - C/C++: 'Program/C++.md'
        - Java: 'Program/Java.md'
        - Python: 'Program/Python.md'
        - Windows: 'Program/Windows.md'
    - Study:
        - Language: 'Study/Language.md'
        - Computer Science: 'Study/ComputerScience.md'
    - Life:
        - Growing: 'Life/Growing.md'
```

### 本地预览

即使用`mkdocs serve`

### 部署

这里选择将其部署到Github Pages。

除了之前介绍过的在本地使用MkDocs自带的`gh-deploy`命令进行部署外，还可以用Github Actions更为一劳永逸。

在项目根目录新建文件夹`.github`，再在其中新建子文件夹`workflows`，再进入该子文件夹新建文件`ci.yml`，即该文件最终目录为`/.github/workflows/ci.yml`

```yml
name: ci
on:
  push:
    branches:
      - master
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: 3.x
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

此后，只要向仓库提交更新，该Action就会对站点进行更新。

## 更多

还可以设置许多其他的选项，比如字体、颜色、右侧“Table of contens”所支持的目录级数等等，详见[官网](https://squidfunk.github.io/mkdocs-material/)。