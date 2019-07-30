---
layout: post
title: MacOS 终端解决方案：Iterm2 + oh my zsh + tmux 
categories: [Tips]
description: 在Github Page上插入并显示图片
keywords: Github Page, 显示图片
---

新入手了一个Macbook pro,上面的终端存在着一些问题，主要是配色不丰富（几乎没有配色），需要较复杂的设置才可以用全局命令调出终端，命令的自动补全也不是很完善。
因此，希望对终端进行重新的配置，基本要求是变成彩色，能够用快捷键呼叫出终端，有命令的补全功能，能够方便地分屏。

# 安装Iterm2

Iterm2比MacOS自带的终端功能更为强大，可以直接去官网下载（http://iterm2.com)，下载完成后可以双击安装，完成安装即可。

为了使得终端颜色更为丰富些，网上教程都推荐安装solarized color,这个配色方案在vim里面也有，的确还挺漂亮的。首先，去http://ethanschoonover.com/solarized
下载颜色文件，进行解压，然后打开Iterm2，按command + , 打开preferences->profiles->default->color->color preset->import,在刚解压的文件中选择Solaried dark
进行import，最后选中import的solarized dark配色方案即可。

设置终端呼出快捷键：通过command + ，打开preference->keys,勾选Hotkey，设置全局呼叫终端的快捷键，系统会要求进行隐私的设置。但是设置完成后，需要按两次快快
捷键才可以呼出终端，不知道是为什么？

Iterm2常用快捷键：

- 切换全屏; command + enter
- 新建窗口：command + T
- 关闭窗口：command + W
- 选择窗口：command + 数字

# 安装oh my zsh

oh my zsh(https://github.com/robbyrussell/oh-my-zsh)是一个优秀的终端开源项目，可以通过下面的命令完成安装。

```shell
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

安装后，需要将zsh设计shell的默认编译器，

```shell
chsh -s /bin/zsh
```

运行下面命令，将自动填充的项目下载到on my zsh文件夹的相应位置，可以实现zsh的自动填充，刚刚使用还是觉得有些变扭，但是使用之后，还是觉得相当不错的。
```shell
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

然后，打开.zshrc文件，在其中增加插件：
```shell
plugins=(zsh-autosuggestions git)
```

当然，还可以对字体，配色等进行修改。我觉得现在的字体还算ok，就没有进行进一步的修改。

# 安装Tmux

Tmux是优秀的终端复用工具，创建多个session，在每个Window中开多个panel,可以左边窗口编写代码，右边窗口进行代码的编译，使用还是非常方便的。

安装非常简单

```shell
brew install tmux
```

安装tmux之后，在zsh终端中输入tmux既可以进去tmux中，tmux的默认前导键为Ctrl + B，一般的操作均需要先按前导键，其中常用的命令有

- 左右分屏：Ctrl + B，然后%
— 上下分屏：Ctrl + B, 然后"

![参考](https://www.jianshu.com/p/c5c5037f72e0)

安装这么多之后，基本可以起飞了。首先，还有很多功能等待探索，然后不应该在这些上面花费太多的精力，还是应当把更多的时间放在算法的研究上。加油，少年！
