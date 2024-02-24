---
title: How To Make ACM TAPS Happy?
description: Tips and tricks to prepare your ACM TAPS submission
date: 2023-02-10
tags:
  - latex
  - writing
  - research
---

{% raw %}

# How To Make ACM TAPS Happy?

Here are some tips and tricks to make ACM TAPS stop complaining about your LaTeX source submission.

### 1. Only use ACM permitted packages

Remove all packages that are not listed in this [webpage](https://authors.acm.org/proceedings/production-information/accepted-latex-packages).

### ~~2. Add short description for `\caption` commands~~

~~For example, change `\caption{This is a figure...}` to `\caption[]{This is a figure...}` in your figures and tables.~~

### 3. Remove `./` in file references

Refer to figure files without `./`. Change `\includegraphics[width=\linewidth]{./figures/dog.pdf}` to `\includegraphics[width=\linewidth]{figures/dog.pdf}` in figure environments.

### 4. Do not use self-defined macros in equations

For example, change `$\mcolor{myred}{x}$` to `$\textcolor[HTML]{E03177}{x}$`.

### 5. Use `\aptLtoX{}` to have different LaTeX code for different targets (PDF and HTML)

Step 1. Add the below `acmart-taps.sty` file in your source directory:

```latex
% "acmart-taps.sty"
% This is a patch file for acmart class file
% Develop by Ranjan
% Dated 2019-03-08
% Below commands and environment is compatible to TAPS system

\newenvironment{imageonly}{}{}
\DeclareOldFontCommand{\bi}{\bfseries\itshape}{\bfseries\itshape}
\makeatletter
\@ifundefined{tabnote}{
  \newenvironment{tabnote}{}{}
  }{}
\newcounter{sidebartable}
\newcounter{figuresidebar}
\newcounter{floatsidebar}
\let\bm\boldsymbol
\def\aptLtoXcmd#1#2{#2}
\long\def\aptLtoX{\@ifnextchar[{\@aptLtoX}{\@aptLtoX[]}}
\long\def\@aptLtoX[#1]#2#3{#3}
\def\aptLtoXdel#1{}
\makeatother

\endinput
```

Step 2. Refer to it in your preamble:

```latex
\usepackage{acmart-taps}
```

Step 3. You can now use `aptLtoXdel` in your LaTeX code when you want to write different code to generate PDF and HTML files. For example, if you only want to change underline color in PDF, you can wraps `\setulcolor{gray}` as below:

```latex
\aptLtoX[graphic=no, type=html]{
  % LaTeX for HTML target
  % Pass
}{
  % LaTeX for PDF target
  \setulcolor{gray}
}
```

### 6. You can use `\looseness=x` in LaTeX

TAPS team might remove `\looseness=x` from your code, but it is actually compatible with TAPS.

### 7. You cannot use `\phantomsection`

TAPS breaks when you have any `\phantomsection`. If you really want to use it, wrap it with `aptLtoX`: e.g., `\aptLtoX[graphic=no, type=html]{}{\phantomsection}`.

### 8. Wrap everything in `CJK*` if you have Chinese/Japanese/Korean characters

TAPS would suggest you wrap the entire document inside the CJK UTF8 environment.

For example, instead of applying CJK\* locally like:

```latex
The translation is \begin{CJK*}{UTF8}{gbsn}``你好''\end{CJK*}.
```

You can wrap the LaTeX document in `CJK*`:

```\latex
\begin{document}
\begin{CJK*}{UTF8}{gkai}

% xxx

\end{CJK*}
\end{document}
```

Also, TAPS is unhappy with CJK characters in the `\Description{}` command. They would suggest you to comment out the `\Description{}` with CJK characters.

### 9. TAPS doesn't like `enumitem`

Wrap your `enumitem` lists (e.g., `itemize` and `enumerate`) in `\aptLtoX`.

For example, change below list:

```latex
\begin{enumerate}[topsep=5pt, itemsep=0mm, parsep=1mm, leftmargin=30pt, label=\textbf{D\arabic*.}, ref=D\arabic*]
    \item\label{item:d1}
    My item 1.
    \item\label{item:d2}
    My item 2.
    \item \label{item:d3}
    My item 3.
\end{enumerate}

```

to a wrapped list with two variants:

```latex
\aptLtoX[graphic=no,type=html]{
% Used for HTML conversion
\begin{enumerate}
  \item [\textbf{D1.}] \label{item:d1}
  My item 1.
  \item [\textbf{D2.}] \label{item:d2}
  My item 2.
  \item [\textbf{D3.}] \label{item:d3}
  My item 3.
\end{enumerate}
}{
% Used for PDF rendering
\begin{enumerate}[topsep=5pt, itemsep=0mm, parsep=1mm, leftmargin=30pt, label=\textbf{D\arabic*.}, ref=D\arabic*]
  \item\label{item:d1}
  My item 1.
  \item\label{item:d2}
  My item 2.
  \item \label{item:d3}
  My item 3.
\end{enumerate}
}
```

### 10. Limited Equation Support

TAPS has limited support for the equation environments. For example, you cannot use `[1em]` to [stretch equation rows](https://tex.stackexchange.com/questions/14679/amsmath-align-environment-row-spacing) in an `align` environment.

You can also not use `aligned` inside an `align` environment to create multi-line rows. A workaround is to use `\nonumber` in these multi-line rows directly.

### 11. Email TAPS to cancel a submission

It usually takes about 4–6 days for TAPS team to debug your failed submissions. If you want to debug yourself (e.g., trying out different workarounds), you can email the TAPS support team to withdraw your failed submission so that you can resubmit again. It only takes about 1–3 hours for TAPS to re-open the submission for you.

### 12. Avoid defining a macro named `link{}`

It seems there is some internal conflict for the macro name `link{}` when used in footers. You can simply rename it to `mylink{}`.

### 13. Avoid defining macros that include `includegraphics{}`

TAPS wants the content to expose any `includegraphics{}` in the main text instead of through macros. For example:

```latex

% BAD
\newcommand*{\vcenteredhbox}[1]{\begingroup\setbox0=\hbox{#1}\parbox{\wd0}{\box0}\endgroup}
\newcommand{\inlinefig}[2]{\vcenteredhbox{\includegraphics[height=#1pt]{figures/#2}}}

\inlinefig{9}{my-fig.pdf}

% OK
\newcommand*{\vcenteredhbox}[1]{\begingroup\setbox0=\hbox{#1}\parbox{\wd0}{\box0}\endgroup}

\vcenteredhbox{\includegraphics[height=9pt]{figures/my-fig.pdf}}
```

### 14. TAPS doesn't support `\xlabel`

If you use `\xlabel`, wrap it and its references in `\aptLtoX`.

### 15. TAPS doesn't support underline configuration from `soul`

If you are setting the underline color and spacing, wrap them in `aptLtoX`. For example:

```latex
\aptLtoX[graphic=no, type=html]{}{
  \setulcolor{grayIII}
  \setul{0.45ex}{0.7pt}
}
```

### 16. TAPS doesn't render underline in HTML

Your `\ul{}` underline will disappear in the HTML version. If you want them to stay in HTML, one way is to replace them with images by using the below command:

```latex
\begin{imageonly}\ul{my text}\end{imageonly}
```

### 17. TAPS doesn't support `arrayrulecolor`

If you are using `arrayrulecolor` to change the cell's border color in a table, wrap the whole table in `aptLtoX` and do not set the border color in the HTML block.

---

### Finally, watch out for hidden errors in the generated HTML file

Congratulations on making it this far! 🎉 Following all these steps, it's highly likely that TAPS can now successfully generate an HTML version of your paper. However, be aware that TAPS may have also introduced some "hidden" HTML-specific errors. Therefore, it's a good idea to skim through the HTML file to catch these errors.

For instance, if you've used `hspace{10px}` in your equation, there may be some random `px` present in your equation within the HTML file. To resolve these issues, you will need to adjust your LaTeX code accordingly (e.g., use `\quad` instead of `hspace{10px}`).

###### Last updated: 2/20/2024

{% endraw %}
