---
title: "现代Unix命令行"
date: "2024-10-13"
toc: true
autonumber: true
readTime: false
math: true
---

近些年来有一些开源的现代命令行工具，主要用于取代同样或者类似功能的传统工具，也出现了一些新型的命令行工具。这些工具的共性是大部分是用`rust`语言编写的。我主是基于Mac OS下在使用这些工具。

## 终端

[Alacritty][alacritty] 可以取代 iTerm2，[Zellij][zellij] 可以取代 `tmux` 和`screen`。[fish][fish]可以取代 `zsh` 或 `bash`，Fish官网声称它是90后年轻人的shell，`fish` 自然要搭配 [`oh-my-fish`][omf] 使用才行。

```bash
brew install --cask alacritty
brew install fish zellij 
```
## 编辑器

[Neovim][nvim] 可取代 `vi` 和 `vim`，需要配套安装 [Lazyvim][lazyvim]，才能更好发挥它的作用。我还是 Emacs 的用户，不过这算是图形界面的工具了，很少会在命令行下使用它，而 `nvim` 则基本都是在终端下启动和使用的。

```bash
brew install nvim
git clone https://github.com/LazyVim/starter ~/.config/nvim
```

## 文档

[tldr][tldr] 和 [cheat][cheat] 目标都是用简短的命令行示例取代冗长`manpages`文档。两者对比而言，`tldr` 内容十分丰富，社区贡献活跃，比 `cheat` 好用很多。

## 现代版

常见命令行工具的现代版，一般是用`rust`或`go`语言重写的加强版，大部分用的`rust`语言：

- `ls`: [`eza`][eza], [`exa`][exa], [`lsd`][lsd]
- `cd`: [`zoxide`][zoxide]
- `cat`: [`bat`][bat]
- `cut`: [`choose`][choose](`choose-rust`)
- `grep`: [`ripgrep`][rg], [`ag`][ag]
- `sed`: [`sd`][sd]
- `find`: [`fd`][fd]
- `du`: [`dust`][dust]
- `df`: [`duf`][duf]
- `top`: [`btop`][btop], [`bottom`][btm], [`glances`][glances], [`gtop`][gtop], [`zenith`][zenith]
- `diff`: [`difftastic`][difft]
- `hexdump`: [`hexyl`][hex]
- `ping`: [`gping`][gping]
- `ps`: [`procs`][procs]
- `curl`: [`curlie`][curlie]
- `dig`: [`doggo`][doggo]

## 新工具

以下这些项目的目的不是替换传统的旧工具，而是新类型的命令行工具。

- [`jq`][jq]: `sed` for JSON
- [`fzf`][fzf]: fuzzy finder
- [`delta`][delta]: syntax-highlighting pager (`git-delta`)
- [`atuin`][atuin]: magical shell history
- [`moreutils`][moreutils]: 一系列的新工具。其中`vidir`非常好用，使用文本编辑器
  进行批量重命名文件和删除文件等操作。
- [`lazygit`][lazygit], [`lazydocker`][lazydocker]: TUI for `git` and `docker`
  
## 参考

- https://github.com/ibraheemdev/modern-unix
- https://jvns.ca/blog/2022/04/12/a-list-of-new-ish--command-line-tools/

[omf]: https://github.com/oh-my-fish/oh-my-fish
[alacritty]: https://github.com/alacritty/alacritty
[zellij]: https://github.com/zellij-org/zellij
[fish]: https://fishshell.com
[nvim]: https://neovim.io
[lazyvim]: https://www.lazyvim.org
[tldr]: https://tldr.sh/
[cheat]: https://github.com/cheat/cheat

[rg]: https://github.com/BurntSushi/ripgrep/
[ag]: https://github.com/ggreer/the_silver_searcher
[eza]: https://github.com/eza-community/eza
[exa]: https://github.com/ogham/exa
[lsd]: https://github.com/lsd-rs/lsd
[bat]: https://github.com/sharkdp/bat
[dust]: https://github.com/bootandy/dust
[duf]: https://github.com/muesli/duf
[fd]: https://github.com/sharkdp/fd
[choose]: https://github.com/theryangeary/choose
[zenith]: https://github.com/bvaisvil/zenith
[btop]: https://github.com/aristocratos/btop 
[glances]:https://github.com/nicolargo/glances
[gtop]: https://github.com/aksakalli/gtop
[btm]: https://github.com/ClementTsang/bottom
[difft]: https://github.com/Wilfred/difftastic
[sd]: https://github.com/chmln/sd
[hex]: https://github.com/sharkdp/hexyl
[zoxide]: https://github.com/ajeetdsouza/zoxide
[gping]: https://github.com/orf/gping
[procs]: https://github.com/dalance/procs
[curlie]: https://github.com/rs/curlie
[doggo]: https://github.com/mr-karan/doggo

[jq]: https://github.com/jqlang/jq
[fzf]: https://github.com/junegunn/fzf
[delta]: https://github.com/dandavison/delta
[moreutils]: https://joeyh.name/code/moreutils/
[atuin]: https://github.com/atuinsh/atuin
[lazygit]: https://github.com/jesseduffield/lazygit
[lazydocker]: https://github.com/jesseduffield/lazydocker
