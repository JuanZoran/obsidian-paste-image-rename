# Obsidian 粘贴图片重命名

> :loudspeaker: 从 1.4.0 起，Paste image rename 变成一个通用的重命名插件，可以处理添加到库中的所有附件。

本插件的灵感来自 Zettlr。Zettlr 在粘贴图片后会弹出重命名输入框，当图片需要清晰命名和管理时非常有帮助。

<details>
  <summary>Zettlr 的粘贴提示</summary>

  ![image](https://user-images.githubusercontent.com/405972/162478462-b5ff4fc9-ade2-4ace-adcb-c6436479a7d9.png)
</details>

Paste image rename 不仅复刻了 Zettlr 的体验，还支持自定义名字生成规则，可以自动按照配置的模版完成重命名。

**目录**
- [基础用法](#基础用法)
- [设置 `imageNameKey` frontmatter](#设置-imagenamekey-frontmatter)
- [为重复名称添加前缀／后缀](#为重复名称添加前缀后缀)
- [批量重命名内嵌内容](#批量重命名内嵌内容)
- [快速批量重命名当前文件中的所有图像](#快速批量重命名当前文件中的所有图像)
- [处理所有附件](#处理所有附件)
- [常见问题](#常见问题)
- [设置说明](#设置说明)

## 基础用法

安装插件后，在任意文档粘贴图片即可弹出提示：
![](images/modal.png)

在 “New name” 输入框中填写名称然后点击 “Rename”（或直接回车），图片会被重命名，文档中引用也会同步更新。

若将 “Image name pattern” 设置为 `{{fileName}}`（1.2.0 以后默认值），新的图片名会与当前文档名称保持一致。
![](images/modal-fileName.png)

## 设置 `imageNameKey` frontmatter

当一个文档要包含许多图片，可以通过 frontmatter 配置统一的 `imageNameKey`：

```
---
imageNameKey: my-blog
---
```

然后粘贴图片，提示中的 “New name” 已经自动带上 `my-blog`，这个值正是 frontmatter 中的 `imageNameKey`。
![](images/modal-with-imageNameKey.png)

你也可以在设置中修改 “Image name pattern” 来自定义命名逻辑。

## 为重复名称添加前缀/后缀

只要已有同名文件，插件就会按配置自动添加编号。

仍以上节为例，粘贴第二张图片时提示最初仍是 `my-blog`，点 “Rename” 就会得到 `my-blog-1.png`，而不是覆盖旧图。

图示说明默认设置的命名流程：
- “Duplicate number at start” 关闭，编号作为后缀；
- “Duplicate number delimiter” 为 `-`，所以添加的是 `-1`、`-2`。

再次粘贴第三张图，若不修改输入框则会变成 `my-blog-2.png`，编号会根据当前目录已有的 `my-blog-?.png` 自动递增。

如果开启 “Auto rename”，每次添加图片都会自动按规则命名，无需人工确认。

## 批量重命名内嵌内容

> 1.3.0 新增

使用命令 “Batch rename embedded files in the current file” 可以一次性处理当前文档引用的图片或其他附件（甚至是笔记）。
![](images/batch-renaming.png)

上图展示了如何将 `foo-1.png`、`foo-2.png`… 等文件批量改成 `bar-1.png`、`bar-2.png`…。

也可以将多张图片重命名为相同名称，插件会自动帮你处理重复逻辑。演示视频：https://i.imgur.com/6UICugX.mp4

## 快速批量重命名当前文件中的所有图像

> 1.5.0 新增

命令 “Batch rename all images instantly (in the current file)” 会根据设置中的 “Image name pattern” 立即重命名当前文档引用的所有图片。

这相当于在批量重命名界面中输入预设参数再执行，适合日常快速整理图像。

⚠️ 该命令没有确认步骤，请谨慎使用。

## 处理所有附件

> 1.4.0 新增

Paste image rename 并非只针对粘贴产生的图像，它也可以处理所有新增的附件（无论是拖入、粘贴，还是其他方式）。

开启设置中的 “Handle all attachments” 即可启用此行为。
![](images/handle-all-attachments-settings.png)

若需要排除某些扩展，可以通过 “Exclude extension pattern” 填写正则表达式。

## 常见问题

- Q：我粘贴了图片，但没有弹出重命名提示。

    A：在 Windows 上，如果直接从文件管理器复制图片（而不是浏览器或图像查看器），Obsidian 会保留原始文件名，而非 “Pasted image …”。这种情况下需要打开 “Handle all attachments” 才能触发重命名。

## 设置说明

- **图像名称模式**

  该模式决定新名称的生成方式。

  **可用变量：**
  - `{{fileName}}`：当前文档的名称，不含 `.md` 后缀。
  - `{{dirName}}`：当前文档所在目录的名称。
  - `{{imageNameKey}}`：来自 frontmatter 的 `imageNameKey` 字段。
  - `{{firstHeading}}`：文档的第一个一级标题。
  - `{{DATE:$FORMAT}}`：以 `$FORMAT` 生成当前日期（Moment.js 格式），如 `{{DATE:YYYY-MM-DD}}`。

  **示例：**
  - `{{fileName}}` → `My note`、`My note-1`、`My note-2`
  - `{{imageNameKey}}` → `foo`、`foo-1`、`foo-2`
  - `{{imageNameKey}}-{{DATE:YYYYMMDD}}` → `foo-20220408`、`foo-20220408-1`、`foo-20220408-2`
- **重复编号显示在前/后**

  启用后重复编号会作为前缀（如 `1-foo.png`），否则默认放在末尾（如 `foo-1.png`）。
- **重复编号分隔符**

  用来连接重复编号。填写 `-` 时，后缀会变成 `foo-1`，前缀则是 `1-foo`。只接受文件名中合法字符。
- **总是添加重复编号**

  如果开启，即使文件名唯一也会附加编号，对需要统一编号格式的场景有用。
- **自动重命名**

  打开后粘贴无需确认即可直接重命名，否则会弹出对话框等待确认。
- **处理所有附件**

  默认只处理 “Pasted image ” 前缀的粘贴图像，开启后所有新建附件都能纳入重命名流程。
- **排除扩展名模式**

  仅在 “处理所有附件” 启用时生效，输入正则表达式可排除特定扩展（仅取第一行）。
- **禁用重命名提示**

  关闭来自插件的 Notice 提示，但 Obsidian 在链接变更时仍可能有系统通知，无法屏蔽。
