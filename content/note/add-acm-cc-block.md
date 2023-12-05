---
title: How to Add CC Block for ACM Preprints?
description: Tips on adding a CC block to your ACM preprints
date: 2023-01-26
tags:
  - latex
  - writing
  - research
---

{% raw %}

## Create a CC Block for ACM Preprints

Here are two methods to create a nice CC copyright block for your ACM preprint.

![](https://i.imgur.com/372xtUH.png)

First, you need to check your `acmart.cls` version (you can find it from the first 50 lines of this file).

## version >= v1.87

The newer acmart style comes with CC support! However, it only works if you are using `noacm` option. You can bypass the constraint by modifying the `acmart.cls` file.

Comment out the following code in `acmart.cls` (line 1937 in v1.87):

```latex
% \ClassError{\@classname}{%
%   Sorry, Creative Commons licenses are\MessageBreak
%   currently not used with ACM publications\MessageBreak
%   typeset by the authors}{Please use nonacm
%      option or ACM Engage class to enable CC licenses}%
```

Then, you can use the `cc` copyright by adding the following code in your source `.tex` file:

```latex
\setcopyright{cc}
\setcctype[4.0]{by}
```

## version < v1.87

We can use `etoolbox` to override the special command `@copyrightpermission` defined in `acmart.cls`.

1. Download `cc-by4.pdf` from [here](https://en.wikipedia.org/wiki/Creative_Commons_license#/media/File:CC_BY_icon.svg)
2. Add the following code in your `.tex` source file (make sure it is before `\maketitle` comamnd)

```latex
\setcopyright{rightsretained}
% .... Other conference info ...

% Add CC figure

\usepackage{etoolbox}
\makeatletter
\tracingpatches
\patchcmd{\maketitle}{\@copyrightpermission}{
   \begin{minipage}{0.3\columnwidth}
     \href{<https://creativecommons.org/licenses/by/4.0/>}{\includegraphics[width=0.90\textwidth]{figures/cc-by4.pdf}}
   \end{minipage}\hfill
   \begin{minipage}{0.7\columnwidth}
     \href{<https://creativecommons.org/licenses/by/4.0/>}{This work is licensed under a Creative Commons Attribution International 4.0 License.}
   \end{minipage}

   \vspace{5pt}
}{}{}

\makeatletter
```

{% endraw %}
