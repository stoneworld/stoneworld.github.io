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
然后我们可以在 https://civitai.com/ 下载想要的模型，导入到进行图片生成，导入路径类似如下，如果是 Lora 模型，则放到 Lora 目录。

![](/images/Pasted%20image%2020230304195439.png)

而后重新启动脚本，类似下面的 prompt 复制下，而后点击生成：

> (8k, best quality, masterpiece:1.2), (realistic, photo-realistic:1.37), ultra-detailed, 1 girl,cute, solo,beautiful detailed sky,detailed cafe,night,sitting,dating,(nose blush),(smile:1.15),(closed mouth) small breasts,beautiful detailed eyes,(collared shirt:1.1), night, wet,business attire, rain,white lace, (short hair:1.2),floating hair NovaFrogStyle,


> paintings, sketches, (worst quality:2), (low quality:2), (normal quality:2), lowres, normal quality, ((monochrome)), ((grayscale)), skin spots, acnes, skin blemishes, age spot, (outdoor:1.6), manboobs, backlight,(ugly:1.331), (duplicate:1.331), (morbid:1.21), (mutilated:1.21), (tranny:1.331), mutated hands, (poorly drawn hands:1.331), blurry, (bad anatomy:1.21), (bad proportions:1.331), extra limbs, (disfigured:1.331), (more than 2 nipples:1.331), (missing arms:1.331), (extra legs:1.331), (fused fingers:1.61051), (too many fingers:1.61051), (unclear eyes:1.331), bad hands, missing fingers, extra digit, (futa:1.1), bad body, NG_DeepNegative_V1_75T,pubic hair, glans


如果调用过程中存在报错点，可以按照提示进行操作。

生成的图片如下：
![](/images/Pasted%20image%2020230304220634.png)
