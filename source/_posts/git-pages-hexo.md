---
title: GitHub/Gitee Pages + Hexo
date: 2018-11-08 18:15:58
categories:
- Technology
- Git
tags:
- Git Pages
toc: true
---

## Requirements

- Node.js
- Git

<!-- more -->

## Install Hexo:

```sh
$ npm install -g hexo-cli
$ npm ls -g --depth=0
/usr/local/lib
├── hexo-cli@1.1.0
└── npm@6.4.1
```

若出现`permission denied`,请在命令前加`sudo`。

## 新建一个网站

[官网](https://hexo.io/zh-cn/docs/setup)给出以下一套命令进行建站。

```sh
$ hexo init <folder>
$ cd <folder>

$ npm install
```

在当前版本的`hexo-cli`，`npm install`不需要执行，在`init`的时候会安装相应的依赖。

## 启动服务器

```sh
$ hexo server
```

默认情况下，访问网址为： http://localhost:4000。

## Theme

默认主题存放于`<folder>/themes`下，为`landscape`。你可以从[主题列表](https://hexo.io/themes/)中挑选自己喜欢的主题，下载并替换。

- 下载
```sh
$ cd themes/
$ git clone https://github.com/probberechts/hexo-theme-cactus.git
$ rm -rf landscape/
```

- 修改`_config.yml`文件:
```yaml
theme: hexo-theme-cactus
```

- 重新启动服务器进行预览
```bash
$ hexo server
```

<!-- [hexo-theme-doc](https://zalando-incubator.github.io/hexo-theme-doc/) -->
