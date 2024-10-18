---
title: "Mac OS 系统配置参考"
date: "2024-10-16"
toc: true
autonumber: true
readTime: false
draft: false
---

最近换了一台新笔记本，是MacBook Pro 2023款，配置为M3 CPU、16G内存、1T硬盘。这是我第三台Mac笔记本了，上一台是2019年买的MacBook Pro（8G+512G），再早一台就是2012年买的MacBook Air丐版了。目前这三台笔记本都还健在，Air是电池废了，其他功能还正常，2019 Pro则是电池鼓包加系统运行缓慢。我不应该每次都更新到最新版系统，新系统在老硬件上运行越来越慢。最近旧笔记本慢到有点无法忍受了，于是换一台新的。从目前使用几周的情况看，新机器运行非常顺畅，M3芯片加16G内存已经完全可以满足我的日常使用需求了。

记录一下我新机器的配置和软件安装情况，未来再换机器时可以参考。

## 系统配置

首先是语言设置，我习惯将电脑和手机的界面语言都设置为英文。另外 Mac OS 默认的一些配置不是我所习惯的，也要做一些调整。在系统配置里，搜索下列关键词就可以修改。

- Tap to click: 触摸板单击配置，默认需要物理按下，改成轻触就行。
- Use trackpad for dragging: 习惯使用三个手指轻触触摸板拖动文件或程序窗口。
- Look up & data detectors: 设置触摸板三指轻触查词典，这个功能非常有用。查英文生词是十分高频的操作，需要有更快捷的方式。
- Show Bluetooth in Menu Bar: 在菜单栏上显示蓝牙图标。
- Show Battery Percentage: 在菜单栏显示电池百分比。
- Speak selection: 启用机器语音朗读选中文本的功能，可用快捷键option-esc启动朗读，支持自动检测不同的语言，这个功能非常强大。
- Keyboard shortcuts: Swap CapsLock and Ctrl，Emacs 用户会习惯将大写锁定键跟控制键功能交换一下，因为控制键使用频率非常高，大写锁定键（在Mac下也是中英文输入法切换键）比控制键宽很多，也更靠近左手小指，适合频繁使用。
  
## 基础软件

最基础必备的软件有输入法、翻墙软件和Homebrew。我使用的输入方式是自然码双拼，输入法是鼠鬚管([Squirrel][squirrel])，搭配雾凇拼音([rime-ice][rime-ice])配置使用。翻墙软件使用的是[Karing][karing]，按年付费买的机场。至于[Homebrew][brew]，最近配置新Mac才发现它用来管理图形界面程序也非常方便，以前只有命令行工具才用`brew`安装，现在只要有`cask`的图形界面程序都使用`brew`安装和管理。这次新系统的配置，完全弃用了原来折腾了一段时间的`nix`，主要遵循能用`brew`安装的软件都用`brew`安装的原则。

`brew`是一款很有个性的软件工具，使用`ruby`编写，它的一些专有名词命名都跟酒有关。比如它自己的命名Homebrew就是家酿啤酒的意思，然后它的一些相关概念的命名也非常形象：

- formula（配方）package definition that build from sources
- cask（酒桶）package definition that installs native app
- keg（小酒桶）directory containing a given formula version
- rack（酒架）directory containing one or more kegs
- cellar（酒窖）directory containing one or more racks
- bottle（酒瓶）pre-built keg instead of building from sources

`brew`默认是从源码编译安装，所以是依赖Xcode命令行工具的。在安装`brew`的时候，它会自动帮你安装上Xcode命令行工具。Xcode Command Line Tools 是由一系列系统必备，尤其是开发者必备的命令行工具组成，包含了C/C++语言的整套编译环境如`clang`, `gcc`等，源代码管理工具`git`，编程语言环境`swift`, `python`等等。在苹果芯片(Apple Silicon)的系统上，`brew`默认安装的位置是`/opt/homebrew`。原因是`brew`编译安装的二进制文件只能支持一种CPU架构，维护者决定原使用的目录`/usr/local`用于安装`x86`架构的二进制文件，新的目录则用于安装`ARM`架构的。

## 常用软件

搞定中文输入、翻墙和`brew`包管理器后，其他最常用的软件有终端、Shell、编辑器和浏览器。

- 终端：首选Alacritty+Zellij，次选Warp，目前是两个都安装了，日常主要使用Alacritty。
- Shell：几年前就从`zsh`转到`fish`了，同时也要安装上`oh-my-fish`，以及安装一种Nerd字体用于在终端显示图标。
- 编辑器：目前是`Emacs`、`Neovim`、`VSCode`都在用，使用最频繁的是`Emacs`，有自己花了不少时间定制的一套配置文件，`Neovim`则是搭配`lazyvim`使用。
- 浏览器：使用Google Chrome，通过账号同步相关配置。

`emacs`编辑器我一般使用最新版源码编译安装，并且定期同步源码更新重编译：

```bash
git clone -v https://github.com/emacs-mirror/emacs.git
 ./configure --with-gnutls=ifavailable --with-native-compilation --with-tree-sitter
make -j8
make install -j8
mv nextstep/Emacs.app /Applications
```

其他的常用软件还有，笔记记录软件使用`obsidian`，电子书管理沿用`zotero`，快捷启动使用`raycast`（之前用`spotlight`，这次发现`raycast`挺好用），RSS阅读器用`netnewswire`，命令行的文档查阅用`tldr`，虚拟化和容器管理使用`orbstack`。

非开源软件类的，办公用Office和WPS，文件同步用`google-drive`和`adrive`（阿里云盘），即时通讯用腾讯的四件套`wechat`, `qq`, `wecom` 和 `tencent-meeting`，图形化的文档查阅工具用Dash，PDF编辑用Adobe Acrobat。

另外值得一提的，发现一个非常好的工具[`chezmoi`][che]，使用Go语言编写，可用于管理命令行相关配置文件，很符合我的使用需求，添加相关配置文件后可以提交和推送git仓库。还有[`Hammerspoon`][hammer]这个工具定制不少实用的系统功能，如连上公司Wifi系统自动静音、休眠自动关闭蓝牙、切换到不同的程序自动切换中英文输入法，还可以用它实现窗口平铺管理的功能，也非常好用。

## 参考

- https://github.com/macdao/ocds-guide-to-setting-up-mac
- https://github.com/nikitavoloboev/config
- https://github.com/jaywcjlove/awesome-mac/blob/master/README-zh.md

[squirrel]: https://github.com/rime/squirrel
[rime-ice]: https://github.com/iDvel/rime-ice
[karing]: https://github.com/KaringX/karing
[brew]: https://brew.sh/
[che]: https://github.com/twpayne/chezmoi
[hammer]: https://www.hammerspoon.org/
