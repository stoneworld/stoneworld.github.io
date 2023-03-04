---
title: "Stable Diffusion 使用教程"
date: 2023-03-03T16:01:23+08:00
lastmod: 2023-03-03T16:01:23+08:00
draft: false
tags: ["AI"]
categories: ["AI"]
authors: 
- "stoneworld"
---

因为我这里是 Mac，所以整个操作流程都是在 Mac 下进行的：

### 安装 [brew](https://brew.sh/)(如果你之前没有安装)

brew 是 **macOS（或 Linux）缺失的软件包的管理器**
将以下命令粘贴至终端：
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

### 安装依赖

我们安装 brew 其实是为了安装 Stable Diffusion 的前置基础依赖，命令如下：

`brew install cmake protobuf rust python@3.10 git wget`

### 下载 Stable Diffusion 

因为这是一个开源的软件，是需要从 github 上下载开源包，命令如下：

`git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui`

![](/images/Pasted%20image%2020230304180536.png)


而后进入到文件夹，执行 `./webui.sh` 脚本，安装相关依赖以及模型，执行示例如下：

![](/images/Pasted%20image%2020230304180859.png)

如果期间出现网络问题或许你需要终端代理，请自行调整，类似如下：

`export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7890`

最终完成后结果如下，而后打开划线的地址：

![](/images/Pasted%20image%2020230304191918.png)

![](images/Pasted%20image%2020230304191943.png)
然后我们可以在 https://civitai.com/ 下载想要的模型，导入到进行图片生成，导入路径类似如下：

![](/images/Pasted%20image%2020230304195439.png)

而后重新启动脚本，类似下面的 prompt 复制下，而后点击生成：

> RAW photo, <lora:liuyifei_10:1>, a close up portrait photo of young chinese woman in wastelander clothes, background is city ruins, 8k uhd, dslr, soft lighting, high quality, film grain, Fujifilm XT3,