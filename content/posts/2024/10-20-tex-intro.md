---
title: "Tex 入门指南"
date: "2024-10-20"
toc: true
autonumber: true
math: true
readTime: false
draft: false
---

今年年初的时候，花了不少时间用`LaTeX`格式写了一篇给自己参考的入门指南，主要用于测试当代`TeX`发行版如`texlive`对中文的支持。这是用`pandoc`转换成`markdown`后微调的版本。

```sh
pandoc -s tex-intro.tex -o tex-intro.md
```

## Tex 简介

TeX 是伟大的计算机科学家 Donald E. Knuth 为写作他的经典书籍《计算机程序设计艺术》 而创造的一个排版引擎，1977 年开始开发，1982 年发布，在 1989 年宣布 TeX 所有功能冻结，后续只修改 Bug。TeX 以卓越的稳定性、跨平台和几乎没有 bug 而著称，尤其是其方便而强大的数学公式排版能力，无出其右者。TeX 读作\"Tech\"，与汉字『泰赫』发音相近。

TeX 系统自诞生以来，主要在两个方向扩展，一是创造了 LaTeX 和 ConTeXt，这些是为了更好地在顶层使用原生系统的扩展，另一个方向是为支持 PDF 输出，创建了 pdfTeX，以及后续的 XeTeX 和 LuaTeX 等。

LaTeX 是对 TeX 的一层封装，最初设计目标是分离内容和格式，使作者能专注内容创作而非版式设计。读作\"Lay-tech\"，与汉字『雷泰赫』发音相近。LaTeX2e 是当前版本，意思是超出第二版未达第三版 LaTeX3。

TeX 衍生工具最大的一次改进，是 20 世纪 90 年代出现的 pdfTeX，作者是越南人Hàn Thế Thành。[^1] 原生TeX系统所产生的排版文档格式叫 DVI(Device Independent Format)，这种格式可转换成 Postscript 格式最终打印出来。1993 年诞生的 PDF格式是比 Postscript更好的一种文档格式，如支持超链接、目录和现代图片格式。pdfTeX 让 TeX 支持直接输出PDF 文档格式。

中国 TeX 圈子比较有名的人物有： pTeX-ng 的作者马起园（知乎李阿玲）和中文 TeX 文档类和宏包 CTeX-kit 的开发者刘海洋。[^2]刘海洋的《LaTeX 入门》是当前国内最重要的LaTeX 参考书之一。

## 基本概念

各种扩展：

-   TeX (`initex`)，使用`tex -ini`启动，只能理解大约 300 来个基本命令（primitive commands）。支持使用基本命令去定义新的命令。

-   plain TeX，`preloaded format=tex`，使用`tex`命令启动， 默认加载了`plain.tex`格式文件(format file)，除基本命令外，还有少量由 Knuth 自己定义的大约600 个宏（macros），是有所用其他扩展的基础。

-  e-TeX ，发布于 1996 年，增加了一些新的基本命令，跟原系统 100% 兼容。是事实上的标准TeX，几乎所有后续的引擎都是在它的基础上开发的。

-   LaTeX(**La**mport **TeX**) 是一个基于 TeX 的更高层次的语言， 于 1985 年发布。Leslie Lamport 基于 Plain TeX 定义了更多宏命令，把众多排版细节用傻瓜式的命令封装起来。除了建立 LaTeX 标准包，还允许开源社区对其扩展，目前已经有成千上万的 LaTeX 包让你实现各种各样的排版。

-   ConTeXt 诞生于 1990 年，作者是 Hans Hagen，项目目标跟 LaTeX 一样，但侧重点不同。

-   pdfTeX 基于 e-TeX，提供命令可将 LaTeX 或 TeX 源代码文件直接编译成 PDF 文件。对 Unicode 的支持不是很好，不支持操作系统的`truetype`字体，只能使用`type1`字体。在西文世界最常用。

-   LuaTeX 在 2007 年创建，基于 pdfTeX 扩展，增加了 Unicode 和现代字体支持，嵌入了 Lua 脚本引擎，让 TeX 更贴近现代编程语言，目标是取代 pdfTeX 。编译速度较慢。[^3]

-   XeTeX 是 2004 年 Jonathon Kew 创建，最初只是为 Mac OS 用户开发，现在已支持多平台。它支持 Unicode，默认输入是 UTF-8，并且支持现代字体技术。中文用户最佳选择。

-   pTeX 系引擎，p 是 publish 的缩写，由日本人创建于 1990 年，对日语支持较好。用得较广泛的是 e-upTeX，支持 UTF-8。pTeX-ng [^4] 是下一代的 pTeX，由中国人马起园（Clerk Ma）开发，是纯 C 编写的，原生支持 Unicode，对现代字体支持良好，可直接输出 PDF。

-   TeX Live 诞生于 1996 年，是由国际 TeX 用户组织 TUG 开发的 TeX 发行版。它是排版引擎、支持排版的文件（基本格式、宏包、字体等）以及一些辅助功能的集合，支持不同的操作系统平台。[^5] MacOS 系统下建议安装基于 TeX Live 定制化的 MacTeX，针对 MacOS系统做了一些配置优化， 并包含了一些 MacOS 特有的程序。

-   MiKTeX 是一个主要用于 Windows 平台的发行版。 MiKTeX 的安装程序只会包含一些基本宏包，而TeX Live 会默认安装所有宏包。

其他概念：

-   **引擎**，全称排版引擎，是编译源代码并生成文档的程序，如 pdfTeX、XeTeX 等，有时也称为编译器。

-   **格式**，是定义了一组命令的代码集。Knuth 本尊写了一个简单的 plain TeX 格式，LaTeX 是使用最为广泛的一个格式。系统启动时会首先加载格式文件（Format file），如 Plain TeX，LaTeX 等。

-   **编译命令**，是实际调用的、结合了引擎和格式的命令，如`tex`、`latex`、`pdflatex`、`xelatex`、`lualatex` 等等。

-   **宏包**，LaTeX 的扩展机制是用户贡献宏包，宏包文件的扩展名是`.sty`，目的是兼容早期的 Stype Option Files。宏包（Macro Package）主要是一些增强或补充 LaTeX功能的扩展，如排版复杂的表格、插入图片、增加颜色和超链接等。

-   **TeX目录结构**（TeX Directory Structure, TDS），有时也称为 `TEXMF`树，取TeX + METAFONT 之意。

    -   `tex/latex`，LaTeX 宏包。
    -   `doc/latex`，LaTeX 宏包的帮助文档。
    -   `source/latex`，LaTeX 宏包的源代码。
    -   `bibtex`，BIBTeX 工具相关文件。
    -   `fonts/tfm`，TFM 格式的字体文件。
    -   `fonts/type1`，POSTScript 字体文件（Type1），PFB 格式。
    -   `fonts/opentype`，OpenType 格式的字体文件。

## 使用理由

文字编辑器是 WYSIWYG (What you see is what you get)，也是 What you see is all you've got。文字编辑器用户一边输入内容一边要调整内容的排版样式，而 TeX 用户只需描述内容的含义，由 TeX 系统进行排版。越复杂的文档，尤其是由大量参考文献和数学公式，越适合使用 TeX 排版。

**十个使用TeX的理由：**[^6]

-   输出质量：TeX 有最好的排版输出质量，尤其是对于一个非职业设计师来说。
-   输出质量：TeX 懂排版，有更复杂的排版算法，尤其对数学公式有更好的排版支持。
-   卓越工程：TeX 运行很快，只占用很少的内存和磁盘空间。
-   卓越工程：TeX 非常稳定可靠，大量的用户，历史深远。作者已经冻结了 TeX 核心引擎，它能继续甚至永远正常工作。
-   卓越工程：TeX 很稳定，但是没有僵化，用户可以在核心引擎上做大量扩展，比如广为流行的 LaTeX。
-   卓越工程：TeX 的输入是纯文本。纯文本跨平台、占用空间小，易于协作、易于搜素，便于自动生成和处理。
-   卓越工程：TeX 的输入可以是任何格式，如 PostScript、PDF 和 HTML 等，尤其对 PDF 的支持非常好。
-   自由：TeX 源码是免费和开放的。
-   自由：TeX 能运行在任何平台。
-   流行：TeX 被大部分科学家，尤其是学院派的科学家使用，是学术出版的标准。

**LaTeX的优点**：

-   专业的排版输出能力，产生的文档看上去就像『印刷品』一样。
-   方便而强大的数学公式排版能力，世间无出其右者。
-   用户只需专注于一些组织文档结构的基础命令，无需操心版面设计。
-   容易生成复杂的专业排版元素，如脚注、交叉引用、参考文献、目录等。
-   强大的可扩展性。数以千计的宏包用于补充和扩展。
-   促使用户写出结构良好的文档。
-   跨平台、免费、开源。

## 使用技巧

-   使用什么编辑器？Mac 下最好的编辑器是 `Texpad`，但是是商业软件。开源软件中最好用的是`TexStudio`。
-   如何区别基本命令和宏命令？
    -   在 TeX解释器中输入 `\show\cmdname` 后，系统会显示命令的定义。
    -   在TeXBook的索引中，基本命令前都会带有星号标识。
-   如何插入空格？ 空格分为两个 quad 空格、quad 空格、大空格、中等空格、小空格、 没有空格和紧贴7种类型，分别有不同的表示方法。

-   如何查看中文文档？
    -   `texdoc lshort-zh-cn`，《一份（不太）简短的介绍》。
    -   `texdoc ctex`，《CTeX宏集手册》。
    -   `texdoc latex-notes-zh-cn`，《LaTeXNotes》 by Alpha Huang。
    -   `texdoc ctxnotes`，《 学习笔记》 by Li Yanrui。

-   TeXLive 如何安装和更新宏包？

        % 查看信息
        tlmgr info <package-name>
        % 安装和卸载
        tlmgr install <package-name>
        tlmgr remove <package-name>
        % 列出所有可更新的宏包
        tlmgr update --list
        % 更新所有宏包（包括 tlmgr本身）
        tlmgr update --all --self
        % 指定国内更新源镜像地址
        % <mirror>格式：https://mirrors.cloud.tencent.com/CTAN
        tlmgr repository set <mirror>/systems/texlive/tlnet

-   提示缺失字体文件。找到字体文件所在目录，并设置：

        sudo tlmgr conf texmf OSFONTDIR <path> 

-   如何提升文档编译速度？ 在文档首行加入`\special{dvipdfmx:config z 0}`可禁止对输出的 PDF压缩，减少压缩所需时间。需要输出正式 PDF 文件时，再注释首行。

## 数学公式

数学公式有两种排版方式，一是与文字混排，称为行内公式，二是单独列为一行排版，称为行间公式。

行内公式由一对` $ `符号包裹，行间公式由` equation `环境包裹。` equation `环境默认带编号，不带编号的行间公式，可以用` displaymath `或者` *equation `环境，或者将公式用` \[ `和` \] ` 包裹。

**数学模式：**

1.  输入的空格会被忽略，数学符号的间距默认由符号的性质决定。
2.  不允许有空行（分段），行间公式也无法用` \\ `命令换行。
3.  所有的字母都被当作数学公式中的变量处理，字母间距与文本模式不一致。

巨算符，包括积分号 $\int$ 和求和号 $\sum$ 等符号。巨算符在行内公式和行间公式的大小和形状有区别。

## 常用宏包

-   `ctex`：用于中文支持。
-   `hyperref`：支持可点击的链接，包括文档内部和外部链接。
-   `metalog, hologo`：输出系统自定义的一些 Logo 符号，如TeX, LaTeX, XeTeX, LuaTeX 等。
-   `listing`：可生成关键字高亮的代码环境。

## 参考书籍

这两本书是系统创造者自己写的，虽然年代比较久远，但经典永不过时：

-   The TeX Book by Donald E. Knuth \@1984
-   LaTeX A Document Preparation System User's Guide and Reference Manual by Leslie Lamport \@1994

Addison-Wesley 出版社的 Tools and Techniques for Computer Typesetting系列书籍：

-   The LaTeX Companion, 2nd edition \@2004
-   The LaTeX Graphics Companion, 2nd edition \@2008
-   Guide to LaTeX, 4th edition \@2004

## 参考链接

-   Comprehensive TeX Archive Network: [ctan.org](https://ctan.org/)
-   TeX Users Group：[tug.org](https://www.tug.org)
-   Chinese TeX Community：[ctex.org](https://www.ctex.org)

[^1]: 参考：<https://www.tug.org/interviews/thanh.html>
[^2]: 参考： <https://tuna.moe/blog/2015/tex/>
[^3]: 官网：<https://www.luatex.org/>
[^4]: 官网： <https://github.com/clerkma/ptex-ng>
[^5]: 官网：<http://tug.org/texlive/>
[^6]: 参见：<https://ctan.org/tex>
