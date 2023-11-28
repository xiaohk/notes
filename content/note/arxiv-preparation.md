---
title: How to Submit Papers to arXiv?
description: Tips on preparing arXiv submissions
date: 2020-04-30
tags:
  - latex
  - writing
  - research
---
{% raw %}

# How to Submit Papers to arXiv?

## Submission Steps

1. Download source code from Overleaf if you use it: `menu` -> `download` -> `source`.

2. Strip comments and combine all `tex` files (`f01-main.tex`, `f02-intro.tex`, etc.) into one file `arxiv_main.tex`.

```[bash]
# Replace f01-main.tex with the main tex file in your overleaf project
latexpand --empty-comments f01-main.tex > arxiv_main.tex
```

3. Create a new directory and copy only essential files into it (`arxiv_main.tex`, style files, figures, etc.).

4. Compile `arxiv_main.tex` with BibTeX in your new directory by running the following four commands. The main purpose of this step is to generate a `.bbl` file. You would need to run `pdflatex` three times. Check if the generated `arxiv_main.pdf` looks correct.

```[bash]
pdflatex arxiv_main
bibtex arxiv_main
pdflatex arxiv_main
pdflatex arxiv_main
```

5. Zip all files inside the directory (don't include the directory itself). Upload the tarball file `arxiv.tar.gz` to arXiv.

```[bash]
tar czvf arxiv.tar.gz ./*
```

## When to Post on arXiv?

1. The "deadlines" of arXiv submission

| Submissions received between (all times Eastern US) | Will be announced (all times Eastern US) | Mailed to subscribers              |
|-----------------------------------------------------|------------------------------------------|------------------------------------|
| Monday 14:00 – Tuesday 14:00                        | Tuesday 20:00                            | Tuesday night / Wednesday morning  |
| Tuesday 14:00 – Wednesday 14:00                     | Wednesday 20:00                          | Wednesday night / Thursday morning |
| Wednesday 14:00 – Thursday 14:00                    | Thursday 20:00                           | Thursday night / Friday morning    |
| Thursday 14:00 – Friday 14:00                       | Sunday 20:00                             | Sunday night / Monday morning      |
| Friday 14:00 – Monday 14:00                         | Monday 20:00                             | Monday night / Tuesday morning     |

2. If you post between **Wednesday 14:00 – Thursday 14:00**, it will be announced on Thursday 20:00 and be on the arXiv front-page for **three days**. This is because the next announcement will be on Sunday 20:00. This is the best day to post on arXiv if you want to maximize the exposure.
3. By default, arXiv orders submissions by "recentness". Therefore, if you submit **right before 14:00**, it is more likely to be listed on the first page of the list (top 25 most recent submissions). On Thursday, there are usually about 70-100 machine learning submissions (3-4 pages!)

## Troubleshooting

1. ⚠️ `latexpand --empty-comments` does not remove comments in `\begin{comment}\end{comment}`. If you write comments other than the native way `%`, you can use [arxiv-latex-cleaner](https://github.com/google-research/arxiv-latex-cleaner) to clean comments.
2. Check out the [checklist for arXiv submission from Overleaf](https://www.overleaf.com/learn/how-to/LaTeX_checklist_for_arXiv_submissions) (Thanks [@bhoov](https://github.com/bhoov))
2. Remember to keep your primary `.tex` file in the root level of the repository
3. `natbib` in an unsorted number style does not work on ArXiv. Solution: remove `natbib` as a dependency, use `\bibliographystyle{unsrt}` instead of `\bibliographystyle{unsrtnat}`

## Best Practices

Now arXiv can successfully compile your LeTeX file! 🎉 Here are some tips to make the generated PDF look great.

1. **Double check the generated PDF.** It's likely that arXiv is using a different LaTeX version from yours, resulting in a different-looking PDF.
   1. If you use PDFLaTeX to compile LaTeX files locally, [you can set `\pdfoutput=1`](https://info.arxiv.org/help/submit_tex.html#pdflatex) in the start of your `arxiv_main.tex` to force arXiv to also use PDFLaTeX.
   2. Sometimes arXiv would mess up with the microtype for full justification papers, especially in section headers and figure captions (e.g., adding a new line). Double check the generated PDF and modify the text when needed. One way to avoid new lines is to wrap your text in `\mbox{}` and force LaTeX to overflow that line.
2. **Add page numbers.** Typically conference style files hide page numbers, so that all accepted papers can bundled into a conference proceeding with a proceeding page number. However, preprints with page numbers are easier to read. Here is a [simple guide](https://notes.zijie.wang/add-paper-page-num.html) to add page number for different popular CS conference LaTeX templates.
3. **Use the correct copyright block** (especially for ACM conferences). Make sure your preprint PDF claims the correct copyright license (e.g., CC, IW3C2W3, rightsretained). For ACM conferences, you can change the copyright block with the command `setcopyright{}`. However, if your paper has a Creative Common (CC) license, you would need [some hacks](https://notes.zijie.wang/add-acm-cc-block.html) to make it work.

{% endraw %}
