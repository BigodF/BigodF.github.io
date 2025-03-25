---
title: "JupyterServer"
date: 2025-03-24T22:31:10+08:00
lastmod: 2025-03-24T22:31:10+08:00
author: ["Bigodf"]

tags:
- python
- jupyter

description: "Jupyter Sever开启服务" # 文章描述，与搜索优化相关
summary: "" # 文章简单描述，会展示在主页
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true
showToc: true # 显示目录
TocOpen: true # 自动展开目录
autonumbering: true # 目录自动编号
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
searchHidden: false # 该页面可以被搜索到
showbreadcrumbs: true #顶部显示当前路径
mermaid: true
cover:
    image: ""
    caption: ""
    alt: ""
    relative: false
---

<!-- more -->


## 设置密码
经过hash加密后，生成的token会保存在用户目录下。

```bash
jupyter notebook password
```

## 启动服务
不同的启动命令，使用的密码不用。

```bash
# 使用上面设置的密码
jupyter notebook --ip=0.0.0.0 --port=10086 --no-browser --notebook-dir=./

# 以下两种方式，会生成一个token，可以在日志中notebook链接中看到，在浏览器中有两种选择，一种是输入这个token、另一种是使用这个token重新设置密码。
python -m jupyter notebook --ip=0.0.0.0 --port=10086 --no-browser --notebook-dir=./
python -m notebook --ip=0.0.0.0 --port=10086 --no-browser --notebook-dir=./
```

## 多版本python问题
如果你有多个python环境，并且这些python环境中也装有jupyter的话，并且出现了juputer启动的python环境不是你当前激活的环境。

可能原因是：当前的虚拟环境没有完整的库，完整的库包括jupyter、notebook。



我遇到的问题是：

系统一个默认python（简称env_1），还有一个用conda安装的环境（简称env_2）。激活env__2后，只安装了jupyter。然后启动jupyter notebook。发现总是报错protobuf的版本过高。

```bash
TypeError: Descriptors cannot not be created directly.
    If this call came from a _pb2.py file, your generated code is out of date and must be regenerated with protoc >= 3.19.0.
    If you cannot immediately regenerate your protos, some other possible workarounds are:
     1. Downgrade the protobuf package to 3.20.x or lower.
     2. Set PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python (but this will use pure-Python parsing and will be much slower).
```

我查看env_2的protobuf库是3.20.3。查看报错过程，发现使用的python是env_1，env_1的protobuf也是5.x的版本。

经过对比发现env_2没有安装notebook库，猜测原因是jupyter在当前虚拟环境没发现notebook后，在env_1中找到了notebook，就是用env_1的环境。具体的查过过程可以参考[https://stackoverflow.com/questions/39007571/running-jupyter-with-multiple-python-and-ipython-paths](https://stackoverflow.com/questions/39007571/running-jupyter-with-multiple-python-and-ipython-paths)。



## 参考
1. [https://stackoverflow.com/questions/39007571/running-jupyter-with-multiple-python-and-ipython-paths](https://stackoverflow.com/questions/39007571/running-jupyter-with-multiple-python-and-ipython-paths)



