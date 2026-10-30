# CMake Cookbook（中文翻译版 · LaTeX 工程）

把 Radovan Bast & Roberto Di Remigio 的 *CMake Cookbook*（Packt, 2018）译为中文，

- 原书：*CMake Cookbook — Building, testing, deploying, and packaging across platforms with CMake*
- 作者：Radovan Bast、Roberto Di Remigio
- 译者：[陈晓伟](https://github.com/xiaoweiChen/CMake-Cookbook)
- 首次出版：2018 年 9 月
- ISBN：978-1-78847-071-1
- 中文名：**CMake Cookbook 中文版**

---

## 目录结构

```
latex_source/                  # 本目录即工程根
├── main.tex                   # 编译入口：\input{book/ccs.tex} + \subfile{book/index.tex}
├── build.bat                  # 一键编译：三遍 xelatex，成功后清理 *.minted 缓存
├── main.pdf                   # 编译成品（504 页）
├── images/                    # 30 张插图（按章分目录）+ cover.jpg + icon_info / icon_warning
├── tools/
│   └── convert.py             # Markdown → LaTeX 转换器（同时生成 book/index.tex）
└── book/
    ├── ccs.tex                # 导言区：自定义命令/环境、minted v3 缓存配置、本项目补丁
    ├── index.tex              # 章节装配（由 tools/convert.py 生成，勿手改）
    ├── manning-pygments-style.tex  # 代码高亮配色
    └── content/
        ├── front/             # 00-cover(封面图) / 00-title(扉页) / preface(前言) / about(关于本书)
        └── main/              # 第 0–16 章：chapter0.tex … chapter16.tex（共 17 章）
```

> 正文源文件必须放 `book/content/` 下：`\subfile` 的相对基准是 `book/`，图片路径仍写 `images/...`（相对 `latex_source/`）。
> 上一层目录另有 Markdown 译稿（`content/`）、原书封面（`cover.jpg`）与插图原图（`images/`）。

---

## 环境依赖

| 依赖 | 版本 / 说明 |
|---|---|
| TeX Live | 2026（含 `xelatex`、minted **v3** / `latexminted`、`tcolorbox`、`tabularx`、`booktabs`、`tikz`） |
| Python | 3.x + **Pygments**（`latexminted` 调它做语法高亮）；本机 `D:\anaconda3` |
| 中文字体 | **Source Han Serif SC（思源宋体）**、**Microsoft YaHei** |
| 拉丁字体 | Cambria、Franklin Gothic Medium |
| 等宽字体 | Consolas（`book/ccs.tex` 里写死 `C:/Windows/Fonts/consola*.ttf`） |

> 字体缺失会导致编译报 "font not found"。思源宋体需本机已安装，微软雅黑为 Windows 自带。

---

## 编译

本项目**使用** `build.bat`（原生 `cmd.exe` 脚本，PATH 已补上 `D:\anaconda3\Scripts` 以便 `latexminted` 找到 Pygments）：

```bat
build.bat          :: 三遍 xelatex，成功后删除 *.minted 缓存
build.bat keep     :: 三遍 xelatex，保留缓存（改稿期间更快）
```

手动编译时**必须**把 minted 缓存目录指成绝对路径（见「已知坑」第 2 条）：

```bash
# Python 根目录 + Scripts 要在 TeX bin 之前，latexminted 才能找到 pygments
export PATH="/d/anaconda3/Scripts:/d/anaconda3:/d/ProgramData/texlive/2026/bin/windows:$PATH"
export TEXMF_OUTPUT_DIRECTORY="ROOT/CMake-Cookbook/latex_source"
export TEXMFOUTPUT="$TEXMF_OUTPUT_DIRECTORY"

xelatex -shell-escape -interaction=nonstopmode -synctex=1 main.tex   # 跑三遍
```

- `-shell-escape` 是必须的（minted 调外部高亮器）。
- **必须三遍**：目录、书签、交叉引用要收敛；封面用 `remember picture` 定位，两遍才到位。
- 耗时取决于代码块数量；首次编译较慢（minted 逐个高亮代码块），建议高频改稿用 `build.bat keep`，出终版用 `build.bat`。
- 排错时给 `xelatex` 加 `-file-line-error`，可直接定位到文件与行号。
- 产物：`main.pdf`（504 页）、`main.aux`、`main.toc`、`main.log`、`main.synctex.gz`。

> 如果环境配置遇到问题或者编译失败，可直接到[ releases](https://github.com/jnulzl/CMake-Cookbook-CN/releases)下载最新的 pdf 文件。

---

## 本书相关

- 译者 GitHub（中文译稿）：<https://github.com/xiaoweiChen/CMake-Cookbook>
- 原书源码（dev-cafe）：<https://github.com/dev-cafe/cmake-cookbook>
- 原书源码（Packt）：<https://github.com/PacktPublishing/CMake-Cookbook>
