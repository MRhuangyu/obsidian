# 使用标注

从 v0.14.0 开始，Obsidian 支持标注块，有时称为"告诫"。标注块是作为块引用编写的，灵感来自 Microsoft Docs 中的"警报"语法。

标注在 [Obsidian Publish](https://help.obsidian.md/Obsidian+Publish/Introduction+to+Obsidian+Publish) 上也原生受支持。

> [!注意]
> 出于兼容性原因，如果您还使用 Admonitions 插件，则应至少将其更新到 v8.0.0，以避免新标注系统出现问题。

使用以下语法表示标注块：。`> [!INFO]`

```
> [!INFO]
> Here's a callout block.
> It supports **markdown** and [[Internal link|wikilinks]].
```

它将显示如下：

> [!INFO]
> Here's a callout block.
> It supports **markdown** and [[Internal link|wikilinks]].


### 类型

默认情况下，有 12 种不同的标注类型，每种类型都有多个别名。每种类型都有不同的背景颜色和图标。

若要使用这些默认样式，请在示例中替换为这些类型中的任何一种。任何无法识别的类型都将默认为"note"类型，除非它们是[自定义的](https://help.obsidian.md/How+to/Use+callouts#Customizations)。类型标识符不区分大小写。`INFO`

-   note
> [!note] 注意
-   abstract, summary, tldr
> [!abstract] 摘要， 摘要， tldr
-   info, todo
> [!info] 信息， 待办事项
-   tip, hint, important
> [!TIP] 提示，提示，重要
-   success, check, done
> [!success] 成功，检查，完成
-   question, help, faq
> [!question] 问题， 帮助， 常见问题
-   warning, caution, attention
> [!warning] 警告，谨慎，注意
-   failure, fail, missing
> [!failure] 失败、失败、缺失
-   danger, error
> [!error] 危险，错误
-   bug
> [!bug] 错误
-   example
> [!example] 例
-   quote, cite
> [!quote] 引用，引用

### 标题和正文

您可以定义标注块的标题，也可以具有不带正文内容的标注。

```markdown
> [!TIP] Callouts can have custom titles, which also supports **markdown**!
```

### 折叠

此外，还可以通过在块后添加（默认展开）或（默认折叠）来创建折叠标注。`+``-`

```markdown
> [!FAQ]- Are callouts foldable?
> Yes! In a foldable callout, the contents are hidden until it is expanded.
```

将显示为：

> [!FAQ]- 标注是否可折叠？  
> 是的！在可折叠标注中，内容在展开之前一直处于隐藏状态。

### 定制

代码段和插件也可以定义自定义标注，或覆盖默认选项。标注类型和图标在 CSS 中定义，其中颜色是元组，图标是任何内部支持的图标（如）的图标 ID。或者，可以将 SVG 图标指定为字符串。`r, g, b``lucide-info`

```css
.callout[data-callout="my-callout-type"] {
    --callout-color: 0, 0, 0;
    --callout-icon: icon-id;
    --callout-icon: '<svg>...custom svg...</svg>';
}
```