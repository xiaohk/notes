---
title: Paper Mark Up
description: Tips on preparing paper revision with latex
date: 2023-11-27
tags:
  - latex
---
{% raw %}

# How to Mark Up Paper Revisions with LaTeX

Below are three steps to highlight changes made in a paper revision with LaTeX.

## Step 1. Generate a submission snapshot

We first take a snapshot of the last version of the LaTeX project before the paper's original submission. If you use Git to version control your project, it is easy to checkout the exact version used for the original submission.

1. Use `latexpand` to flatten the LaTeX structure. It combines all LaTeX files into one file.

```bash
# After submission, before revision
latexpand --empty-comments f01-main.tex > submission.tex
```

2. If you are updating any figure, give it a new name (e.g., `figures/{figure-name}-old.pdf`) and update its reference in `submission.tex`. We need to have both the old figure and new figure to show the differences in Step 3.

## Step 2. Generate a revision snapshot

Once you have made all the revisions, you can take a snapshot of the latest version similar to Step 1.

```bash
# After revision
latexpand --empty-comments f01-main.tex > revision.tex
```

## Step 3. Highlight the changes

We use `latexdiff` to highlight the changes made during the revision.

1. Copy your `submission.tex` and all old figures (e.g., `figures/{figure-name}-old.pdf`) to your current project directory.

2. Create three configuration files for `latexdiff`.

You can copy my configuration into three files `diff-preamble.tex`, `diff-config.cfg`, and `diff-safecomp.cfg`. You can learn more about the configurations on [latexidff's documentation](https://mirrors.mit.edu/CTAN/support/latexdiff/doc/latexdiff-man.pdf).

**File 1:** `diff-preamble.tex`

This file defines how to style the additions and deletions.

```latex
% [diff-preamble.tex]

\RequirePackage[normalem]{ulem}
\RequirePackage{color}\definecolor{RED}{rgb}{1,0,0}\definecolor{BLUE}{rgb}{0,0,1}
\providecommand{\DIFadd}[1]{{\protect\color{BLUE}#1}}
\providecommand{\DIFdel}[1]{{\protect\color{RED}\sout{#1}}}
\providecommand{\DIFaddbegin}{}
\providecommand{\DIFaddend}{}
\providecommand{\DIFdelbegin}{}
\providecommand{\DIFdelend}{}
\providecommand{\DIFaddFL}[1]{\DIFadd{#1}}
\providecommand{\DIFdelFL}[1]{\DIFdel{#1}}
% I don't like the default undercurl for added text, so I commented it out below
% \providecommand{\DIFdelFL}[1]{}
\providecommand{\DIFaddbeginFL}{}
\providecommand{\DIFaddendFL}{}
\providecommand{\DIFdelbeginFL}{}
\providecommand{\DIFdelendFL}{}
```

**File 2:** `diff-config.cfg`

This file tells `latexdiff` to show the deleted image in the original scale.

```bash
SCALEDELGRAPHICS=1
```

**File 3:** `diff-config.cfg`

This file tells `latexdiff` what are the "safe commands" that it can check textual differences. If you define any macros that wrap some text, you can put it in the list below. For example, `myquote` is a macro of `\newcommand*\myquote[1]{``\textit{#1}''}`.

```text
myquote
multirow
```

3. Generate `diff.tex` using `latexdiff`.

```bash
latexdiff submission.tex revision.tex > diff.tex --graphics-markup=2 -p diff-preamble.tex --append-safecmd=diff-safecmd.cfg --config=diff-config.cfg
```

4. Generate `diff.pdf` from `diff.tex`.

```bash
pdflatex diff
bibtex diff
pdflatex diff
pdflatex diff
```

🎉 Open `diff.pdf`, you should see all revision changes to text and figures being highlighted.

5. [Recommended] Finally, to speed up the generation process, you can combine all commands into a bash script and run it instead: `zsh diff-generate.sh`.

```bash
# [diff-generate.sh]

latexpand --empty-comments f01-main.tex > revision.tex

latexdiff submission.tex revision.tex > diff.tex --graphics-markup=2 -p diff-preamble.tex --append-safecmd=diff-safecmd.cfg --config=diff-config.cfg

pdflatex diff
bibtex diff
pdflatex diff
pdflatex diff
```

## Bonus

I also like to use `\usepackage{todonotes}` to highlight specific reviewer comments next to my changes. To do that, I only need to add `\todo{}` in your new LaTeX files.

For example, below is an example of the marked-up revision for our CHI'23 paper [GAM Coach](https://dl.acm.org/doi/10.1145/3544548.3580816) using `latexdiff` and `todonotes`.

[<img src="https://i.imgur.com/FeDTn7c.png" width="100%">](https://dl.acm.org/doi/10.1145/3544548.3580816)




{% endraw %}