---
title: How to add a teaser figure using ACL templates?
description: Hacks to add a teaser figure on the first page using ACL's LaTeX template
date: 2024-03-14
tags:
  - latex
  - writing
  - research
---

{% raw %}

# How to add a teaser figure using ACL templates?

It is quite common to include a teaser figure in ACM and IEEE conference papers.
This figure typically appears after the author information and before the abstract (see this [CHI paper](https://arxiv.org/abs/2402.15350) for an example).

However, it is not trivial to add a teaser figure using the [\*ACL LaTeX templates](https://github.com/acl-org/acl-style-files/) (for ACL, EMNLP, NAACL, EACL, etc.). To do that, we will use a workaround.
<img width="100%" src="https://github.com/poloclub/wordflow/assets/15007159/15925a40-f443-41ad-a65f-c49613556d84"></img>

## If your template is after 2024

If you are using an `acl.sty` template file distributed after 2024, add the below code in your `.tex` file before the `\begin{document}` command.

```latex

\makeatletter
\AtBeginDocument{
\let\@oldmaketitle\@maketitle
\renewcommand{\@maketitle}{\@oldmaketitle
  \vspace{-27pt}
  \includegraphics[width=\linewidth]{./figures/teaser.pdf}
  \vspace{-18pt}
  \captionof{figure}{
    \tool{} empowers machine learning researchers and domain experts to easily explore and interpret \textit{millions} of embedding vectors across different levels of granularity.
    Consider the task of investigating the embeddings of all 63k natural language processing paper abstracts indexed in ACL Anthology from 1980 to 2022.
    \textbf{(A) The Map View} tightly integrates a contour layer, a scatter plot, and automatically-generated multi-resolution embedding summaries to help users navigate through the large embedding space.
    \textbf{(B) The Search Panel} enables users to rapidly test their hypotheses through fast full-text embedding search.
    \textbf{(C) The Control Panel} allows users to customize embedding visualizations, compare multiple embedding groups, and observe how embeddings evolve over time.
  }
  \label{fig:teaser}
  \vspace{17pt}
 }
}
\makeatother

```

## If your template is before 2024

If you are using an `acl.sty` template file distributed before 2024, add the below code in your `.tex` file before the `\begin{document}` command.

```latex
\makeatletter
\let\@oldmaketitle\@maketitle
\renewcommand{\@maketitle}{\@oldmaketitle
  \vspace{-27pt}
  \includegraphics[width=\linewidth]{./figures/teaser.pdf}
  \vspace{-18pt}
  \captionof{figure}{
    \tool{} empowers machine learning researchers and domain experts to easily explore and interpret \textit{millions} of embedding vectors across different levels of granularity.
    Consider the task of investigating the embeddings of all 63k natural language processing paper abstracts indexed in ACL Anthology from 1980 to 2022.
    \textbf{(A) The Map View} tightly integrates a contour layer, a scatter plot, and automatically-generated multi-resolution embedding summaries to help users navigate through the large embedding space.
    \textbf{(B) The Search Panel} enables users to rapidly test their hypotheses through fast full-text embedding search.
    \textbf{(C) The Control Panel} allows users to customize embedding visualizations, compare multiple embedding groups, and observe how embeddings evolve over time.
  }
  \label{fig:teaser}
  \vspace{17pt}
 }
\makeatother
```

## Final Result

After the above step, you should see a teaser figure displayed on the first page! For an example, see this [ACL 2023 paper](https://aclanthology.org/2023.acl-demo.50/).

<img width="100%" src="https://github.com/poloclub/wordflow/assets/15007159/c18c4235-daf9-4d6e-8aa0-c5f04afc5f3a"></img>

{% endraw %}
