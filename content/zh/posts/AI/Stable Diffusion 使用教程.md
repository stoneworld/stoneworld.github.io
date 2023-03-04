因为我这里是 Mac，所以整个操作流程都是在 Mac 下进行的：

### 安装 [brew](https://brew.sh/)

brew 是 **macOS（或 Linux）缺失的软件包的管理器**
将以下命令粘贴至终端：
`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

### 安装依赖

我们安装 brew 其实是为了安装 Stable Diffusion 的前置依赖，命令如下：

`brew install cmake protobuf rust python@3.10 git wget`

