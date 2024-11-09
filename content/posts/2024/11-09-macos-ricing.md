---
title: "MacOS Ricing"
date: "2024-11-08"
toc: true
autonumber: true
readTime: false
draft: false
---

所谓 MacOS Ricing，也就是深度定制化MacOS操作系统的使用界面，图形界面主要是对窗口管理和菜单栏的定制，命令行界面则主要是对终端工具的定制。在我目前的配置里，GUI工具主要使用`sketchybar`和`yabai`，CLI工具主要使用`alacritty`、`zellij`和`fish`，另外还有一个通用的快捷键定制工具`skhd`，跟`yabai`是同一个作者，主要功能就是使用文本文件管理快捷键的配置，也非常好用。

![ricing](/imgs/mac-ricing.webp)

## 菜单栏

使用`sketchybar`定制菜单栏的显示，基于软件作者本人的[配置文件](https://github.com/FelixKratz/dotfiles)稍有一些修改。

```lua
-- 安装相关依赖
sh ~/.config/sketchybar/helpers/install.sh

-- 菜单栏颜色改为透明
-- ~/.config/sketchybar/bar.lua
color = transparent

-- 修改日期显示，只显示星期和时间
-- ~/.config/sketchybar/items/calendar.lua
cal:set({ icon = os.date("%a %H:%M") })

-- 修改活动space颜色为蓝色
-- ~/.config/sketchybar/items/space.lua
highlight_color = colors.blue
```
## 窗口

根据`yabai`的官方[wiki](https://github.com/koekeishiya/yabai/wiki)安装，安装需要
关闭系统完整性保护SIP（System Integrity Protection），需重启系统进行配置，略麻烦
一点。SIP有一个行为，会影响某些场景下软件运行的性能，在系统在运行任何软件之前，会把当前执行文件做一个校验和，然后通过网络请求发送给苹果服务器来检测是否恶意软件。

```bash
# 关闭SIP部分保护项目
$ csrutil enable --without fs --without debug --without nvram

❯ csrutil status
System Integrity Protection status: unknown (Custom Configuration).

Configuration:
        Apple Internal: disabled
        Kext Signing: enabled
        Filesystem Protections: disabled
        Debugging Restrictions: disabled
        DTrace Restrictions: enabled
        NVRAM Protections: disabled
        BaseSystem Verification: enabled
        Boot-arg Restrictions: disabled
        Kernel Integrity Protections: enabled
        Authenticated Root Requirement: enabled

This is an unsupported configuration, likely to break in the future and leave your machine in an unknown state.
```

至于`yabai`的配置，也是在作者[本人配置](https://github.com/koekeishiya/dotfiles)的基础上做扩展修改。我不习惯使用自动平铺窗口模式，默认配置还是使用传统的浮动窗口，同时也开启系统自带的Stage Manager窗口管理模式。当前主要使用`yabai`对窗口进行一些快速操作，用`skhd`绑定一些常用快捷键。

```shell
# 不适用平铺模式
yabai -m config layout float

# borders配置
# inactive_color=0x00000000 is a temp fix for stage manager
borders active_color=0xffb39df3 inactive_color=0x00000000 width=5.0 &
```

最常用到的`skhd`快捷键配置如下：

```sh
# 隐藏/显示sketchybar菜单栏
alt + shift - space: ~/.config/skhd/scripts/toggle_sketchybar.sh

# 切换space
alt - tab : ~/.config/skhd/scripts/space_cycle_next.sh

# 常用控制操作
# float / unfloat window and restore position
alt - t : yabai -m window --toggle float --grid 4:4:1:1:2:2
# floating window fill fullscreen
ctrl + cmd - up     : yabai -m window --grid 1:1:0:0:1:1
# floating window fill left-half of screen
ctrl + cmd - left   : yabai -m window --grid 1:2:0:0:1:1
# floating window fill right-half of screen
ctrl + cmd - right  : yabai -m window --grid 1:2:1:0:1:1

# 脚本内容
❯ cat scripts/space_cycle_next.sh
#!/bin/bash

info=$(yabai -m query --spaces --display)
last=$(echo $info | jq '.[-1]."has-focus"')

if [[ $last == "false" ]]; then
    yabai -m space --focus next
else
    yabai -m space --focus $(echo $info | jq '.[0].index')
fi

❯ cat scripts/toggle_sketchybar.sh
#!/usr/bin/env bash

# Call sketchybar and capture the output
output=$(sketchybar --query bar)

# Use jq to parse 'hidden' and 'height' fields
hidden=$(echo "$output" | jq -r '.hidden')
height=$(echo "$output" | jq -r '.height')

# Check the value of 'hidden' and execute commands accordingly
if [ "$hidden" = "off" ]; then
    sketchybar --bar hidden=on
    yabai -m config external_bar all:0:0
elif [ "$hidden" = "on" ]; then
    sketchybar --bar hidden=off
    yabai -m config external_bar all:${height}:0
fi
```

## 终端

终端主要是使用`alacritty`取代`iterm2`，用`zellij`取代`tmux`。`alacritty`的配置主
要是字体，另外默认App图标比较丑，需要换一个。`zellij`相关配置要调整更多，一方面
取消界面显示的过多冗余信息，另一方面配置下自动重命名tab。至于`fish`，其实默认配
置就很不错了，主要只是增加了`starship`工具来配置命令提示符。

`alacritty`配置内容：

```toml
❯ cat alacritty.toml
live_config_reload = true

[font]
size = 14
normal = {family = "Liga SFMono Nerd Font", style="Regular"}
bold = {family = "Liga SFMono Nerd Font", style="Bold"}
italic = {family = "Liga SFMono Nerd Font", style="Italic"}

[window]
startup_mode = "Windowed"
decorations = "buttonless"
dynamic_padding = true
opacity = 0.9
padding = { x = 25, y = 20 }

[shell]
program = "/opt/homebrew/bin/fish"
args = ["-l", "-c", "zellij attach --index 0 || zellij"]
```

`zellij`主要基于默认配置做一些修改。配置文件的格式为`kdl`，有点陌生的文件格式。

```bash
# 精简界面信息
ui {
    pane_frames {
        rounded_corners true
        hide_session_name true
    }
}
simplified_ui true
default_layout "compact"

# 自动重命名tab配置
vi ~/.config/fish/config.fish

function zellij_tab_name_update --on-variable PWD
    if set -q ZELLIJ
        set tab_name ''
        if git rev-parse --is-inside-work-tree >/dev/null 2>&1
            set git_root (basename -s .git (git config --get remote.origin.url))
            set git_prefix (git rev-parse --show-prefix)
            set tab_name "$git_root/$git_prefix"
            set tab_name (string trim -c / "$tab_name") # Remove trailing slash
        else
            set tab_name $PWD
            if test "$tab_name" = "$HOME"
                set tab_name "~"
            else
                set tab_name (basename "$tab_name")
            end
        end
        command nohup zellij action rename-tab $tab_name >/dev/null 2>&1 &
    end
end

```
