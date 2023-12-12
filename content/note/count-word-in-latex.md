---
title: How to Correctly Count Words in LaTeX?
description: Tips on counting words in LaTeX (especially for ACM CHI submissions)
date: 2023-11-27
tags:
  - latex
  - writing
  - research
  - chi
---

{% raw %}

# How to Correctly Count Words in LaTeX?

Some venues like ACM CHI have started to impose a word limit for paper submissions. We can use the `texcount` package to count words in LaTeX. Overleaf also uses this package, so this note applies to Overleaf users as well.

To use `texcount`, run the following command in the terminal. By default, `texcount` excludes captions of tables and figures and bibliography. `-inc` parses all included files. `-total` reports the total word count instead of word count per file.

```latex
texcount f01-main.tex -inc -total
```

Below are some tips to avoid over-counting the words in LaTeX.

## Tip 1: Avoid Counting Words in Custom Macros

By default, `textcount` counts words in all custom macros. However, some macros do not render text into the PDF, and their content should not be counted. An example is the `Description{}` macro from the [`acmart`](https://ctan.org/pkg/acmart?lang=en) template. Authors are required to use this macro to provide alt text for figures and tables, and the alt text is not shown in the generated PDF. To escape this macro, add the following comment in the main LaTeX file.

```latex
%TC:macro \Description [ignore]
```

## Tip 2: Escape Preambles

Many LaTeX projects include a long preamble file that defines new macros and configures packages. By default, `textcount` counts words from preambles. To escape preambles, add the following comment in the main LaTeX file.

```latex
%TC:ignore
\input{f00-preamble}
%TC:endignore
```

## Tip 3: Escape Non-rendering and Appendix Sections

Some sections are not rendered in the PDF, such as the acknowledgment section during the submission stage `\begin{acks}` and the appendix sections `\appendix`. To escape these sections, just put the `%TC:ignore` comments surrounding these sections.

```latex
%TC:ignore
\begin{acks}
  This work was supported by xxx.
\end{acks}

\appendix
\input{f12-appendix.tex}
%TC:endignore
```

{% endraw %}
