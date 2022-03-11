---
{"created":"2021-12-11T14:52:30 (UTC +08:00)","tags":[],"source":"https://www.zybuluo.com/fyywy520/note/82980","author":"Jiawei Zhang","dg-publish":true,"dg-permalink":"markdown-latex-tips","permalink":"/markdown-latex-tips/"}
---

# Markdown下LaTeX公式、编号、对齐 - 作业部落 Cmd Markdown 编辑阅读器

BY：[@悦华](http://www.weibo.com/quentinchen)

*LaTeX*的数学公式主要有两种，即行内公式（Inline Formulas）和块级公式（Display Formulas）。行内公式内嵌于正文文本中间，与正文文字行高相等；块级公式则单独成行。

## 行内公式

用 `$...$` 符号包围的*LaTeX*代码：

这样的代码可以生成如 $x^n+y^n=z^n$ 这样的行内公式。

## 块级公式

用`$$...$$`符号包围的*LaTeX*代码：

这样的代码可以生成如 $$x^n+y^n=z^n$$ 这样的块级公式。


### 块级公式的编号

直接使用块级代码`$$x^n+y^n=z^n$$`不会生成编号，而使用`\tag{...}`标签就可以生成对应的编号。

这样的代码可以生成如 $$x^n+y^n=z^n \tag{1.1}$$ 的编号块级公式。


除了手动编号之外*LaTeX*还支持对公式的自动编号，对于需要自动编号的公式，需要使用 `\begin{equation}...\end{equation}` 将代码快包围起来。

这样的代码可以生成如

$$\begin{equation}
x^n+y^n=z^n
\end{equation}$$

因为自动编号的代码较为复杂，而且不易扩展，所以不太建议使用自动编号，手动编号更易维护（CMDMarkdown、Obsidian并不支持自动编号）。

公式引用时候可以直接用 `$编号$` 即可。  
对于 $$a^2+b^2=c^2 \tag {1.2}$$ 由公式 $(1.2)$ 即可得到结论。


### 单个公式换行

单个公式很长的时候需要换行，但仅允许生成一个编号时，可以用`split`标签包围公式代码，在需要转行的地方使用`\\`，每行需要使用1个`&`来标识对齐的位置，结束后可使用`\tag{...}`标签编号。

$$
\begin{split}
a &= b \\
c &= d \\
e &= f
\end{split}\tag{1.3}
$$


注意：每行只允许出现一个`&`，使用`split`标签后，编号会**上下居中**显示。

### 多行的独立公式

有时候需要罗列多个公式，可以用`eqnarray*`标签包围公式代码，在需要转行的地方使用`\\`，每行需要使用2个`&`来标识对齐位置，两个`&...&`号之间的是公式间对齐的位置，每行公式后可使用`\tag{...}`标签编号：

$$
\begin{eqnarray*}
x^n+y^n &=& z^n \tag{1.4} \\
x+y &=& z \tag{1.5}
\end{eqnarray*}
$$
