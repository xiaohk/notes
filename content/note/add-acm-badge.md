---
title: Add ACM Badges to ACM Preprints
description: Simple steps to add ACM artifact badges to your ACM preprints
date: 2024-05-10
tags:
  - latex
  - writing
  - research
---

{% raw %}

# Add ACM Badges to ACM Preprints

ACM conferences have begun to award [artifact badges](https://www.acm.org/publications/policies/artifact-review-and-badging-current) for publications containing artifacts like software and datasets. These badges, such as "Artifact Available" and "Artifact Functional," are granted based on the conference's artifact review procedures.

Typically, authors receive these badges on their camera-ready papers after submission, with the badges appearing on the top right of the paper PDFs in the conference proceedings.

However, many authors also wish to include these badges in their preprint PDFs, such as those distributed on arXiv. In this note, we will explore how to add these badges to our own PDFs using the ACM's LaTeX template.

<img src="https://i.imgur.com/9QwLUoc.png" width="100%">

## Step 1: Download Badge Figures

Download the icons (PDF format) for the badges granted to your paper. Save these icons to your paper's LaTeX source directory.

|                 Artifact Available                  |                 Artifact Functional                  |                 Artifact Reusable                  |                 Results Reproduced                 |
| :-------------------------------------------------: | :--------------------------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
| <img src="/img/webp/artifacts-available-v1.1.webp"> | <img src="/img/webp/artifacts-functional-v1.1.webp"> | <img src="/img/webp/artifacts-reusable-v1.1.webp"> | <img src="/img/webp/results-reproduced-v1.1.webp"> |
|  [Download](/img/pdf/artifacts-available-v1.1.pdf)  |  [Download](/img/pdf/artifacts-functional-v1.1.pdf)  |  [Download](/img/pdf/artifacts-reusable-v1.1.pdf)  |  [Download](/img/pdf/results-reproduced-v1.1.pdf)  |

## Step 2: Enable Badges in LaTeX

Add the following code to your LaTeX file (before `\begin{document}`). You might want to modify the path to the downloaded badge files (`figures/artifacts-available-v1.1.pdf`).

```latex
\acmBadgeR[https://www.acm.org/publications/policies/artifact-review-and-badging-current]{figures/artifacts-available-v1.1.pdf}
```

Then, the badge should show up in the compiled PDF!

<img src="https://i.imgur.com/CjjmJd6.png" width="100%">

If your paper has multiple badges, you can add have multiple `\acmBadgeR` calls.

```latex
\acmBadgeR[https://www.acm.org/publications/policies/artifact-review-and-badging-current]{figures/artifacts-available-v1.1.pdf}
\acmBadgeR[https://www.acm.org/publications/policies/artifact-review-and-badging-current]{figures/artifacts-functional-v1.1.pdf}
```

<img src="https://i.imgur.com/sZ6lGSD.png" width="100%">

## Step 3 (Bonus): Style the Badges

By default, the badges in our own PDFs are smaller than the ones in ACM proceedings. If we want our PDFs to look the same as the proceedings, we need to modify the ACM style file (`acmart.cls`).

Search `\setlength\@ACM@badge@width` in the file, and change it as the following:

```latex
% [OLD] \setlength\@ACM@badge@width{3pc}
\setlength\@ACM@badge@width{65pt}
```

Also change the `raisebox{}` in the definition of the command `acmBadgeR`:

```latex
\newcommand\acmBadgeR[2][]{%
  \ifx\@acmBadgeR\@empty
    \gdef\@acmBadgeR{%
      \smash{%
        % [OLD] \raisebox{0.5\height}{%
        \raisebox{-10pt}{%
          \href{#1}{\includegraphics[width=\@ACM@badge@width]{#2}}}}}%
  \else
    \g@addto@macro{\@acmBadgeR}{%
      \hspace{\@ACM@badge@skip}%
      \smash{%
        % [OLD] \raisebox{0.5\height}{%
        \raisebox{-10pt}{%
          \href{#1}{\includegraphics[width=\@ACM@badge@width]{#2}}}}}%
  \fi}
```

After re-compiling the PDF, the badges should have a similar size to the badges appeared in proceeding PDFs.

<img src="https://i.imgur.com/AkBA0fm.png" width="100%">

{% endraw %}
