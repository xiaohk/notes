---
title: Simple Methods to Add Page Numbers in Your CS Papers
description: Tips on adding page numbers in your CS papers
date: 2023-02-15
tags:
  - latex
  - writing
  - research
---

{% raw %}

# Simple Methods to Add Page Numbers in Your CS Papers

<table>

<tr>
  <td> Conference </td> <td> Method </td>
</tr>

<tr>
<td>

[ACM conferences](https://www.aclweb.org/portal/) (e.g., CHI, KDD, WebConf, CIKM that use the [`acmart` LaTeX style](https://github.com/borisveytsman/acmart))

</td>
<td>

Use `\settopmatter{printfolios=true}` right after `\documentclass`.

```latex
\documentclass[sigconf, screen]{acmart}
\settopmatter{printfolios=true}
```

</td>
</tr>

<tr>
<td>

[ACL conferences](https://www.aclweb.org/portal/) (e.g., ACL, NAACL, EACL, EMNLP)

</td>
<td>

Comment out `\pagestyle{empty}` in the conference style file (e.g., `acl2023.sty`)

```latex
\ifacl@finalcopy
    % \thispagestyle{empty}
    % \pagestyle{empty}
    \pagenumbering{arabic}
\else
    \pagenumbering{arabic}
\fi
```

</td>
</tr>

<tr>
<td>

[IEEE VIS](https://www.aclweb.org/portal/) (or [TVCG](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=2945) and some other IEEE conferences)

</td>
<td>

Use the `preprint` option for the `vgtc` LaTeX style file.

```latex
\documentclass[preprint]{vgtc}
```

</td>
</tr>

<tr>
<td>

[NeurIPS](https://www.aclweb.org/portal/)

</td>
<td>

Use the `preprint` or the `final` option for the `neurips` LaTeX style file.

```latex
\usepackage[preprint]{neurips_2022}
\usepackage[final]{neurips_2022}
```

</td>
</tr>

</table>

{% endraw %}
