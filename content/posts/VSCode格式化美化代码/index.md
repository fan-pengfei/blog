---
title: "VSCode格式化美化代码"
date: 2021-08-25T11:04:25.000Z
draft: false
tags: []
---
> vscode代码美化；

1）文件 —> 首选项

> 因为 VSCode 默认启用了根据文件类型自动设置tabsize的选项，在设置中添加：

```bash
"editor.detectIndentation": false
```

2）编辑器配置

> 在项目文件中新建 .editorconfig 文件为特定类型文件指定缩进大小、缩进类型（空格，或tab），是否自动插入末行等等。

```bash
root = true

[*]
charset = utf-8
indent_style = tab
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true

[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

3）安装 VsCode 格式化代码插件

> 搜索并安装 Beautify 格式化代码插件
> 打开要格式化的文件 —> F1 —> Beautify file —> 选择你要格式化的代码类型

4）格式化对齐快捷键：

- Windows： `Ctrl` + `K` + `F`

- Windows：`Shift` + `Alt` + `F`

- Mac： `Shift` + `Option` + `F`

- Ubuntu： `Ctrl` + `Shift` + `I`
