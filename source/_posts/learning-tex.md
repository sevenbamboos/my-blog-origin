---
title: Learning TeX
date: 2017-10-17 22:59:56
categories: Lifestyle
tags:
- TeX
---

# Q&A
Question | Answer
--- | ---
01.Preamble | The part before `\begin{document}`. It's for `\title`, `\author` and `\newcommand` that is visible for the whole document.
02.Define commands(macro) | `\newcommand{command-name}[parameter-count][optional-parameters]{definition}` in preamble
03.Show title and table-of-contents | Make sure both of them are after `\begin{document}`
04.Article outline elements | `\chapter \section \subsection`
05.Numbered list | `\begin{enumerate} \item ... \end{...}`
06.Non-numbered list | `\begin{itemize} \item ... \end{...}`
07.Description list | `\begin{description} \item ... \end{...}`
08.Table | `\begin{tabular} head1 & head2\\ \hline row1-col1 & row1-col2\\ \end{...}` Don't forget \\ at the end of each row (including head)
09.Reference | `\label{key} \ref{key} \pageref{key}`
10.Bibliography | `\begin{thebibliography}{widest label} \bibitem[labelA]{keyA} Author A, Title A, Year A \end{...}` Don't forget the second argument in begin section. `\cite{keyA,keyB}` to cite the reference(s).
11.Inline formula | `\( ... \)`
12.Separate formula | `\[ ... \]`
13.Numbered formula | `\begin{equation} ... \end{...}`
14.Multi-line formula | `\begin{align} formula1 &= A formula2 &= B \end{...}` amsmath provides a `align` mode to use `&` to align multi-line formula. 
15.Subscript in formula | a_{subscript} `\lim_{n=1,2, \ldots} a_n`
16.Underline and overline in formula | `\overline{} \underline{}_{} \overbrace \underbrace`

See [PDF](2017/10/17/learning-tex/learning-tex.pdf) and [TEX](2017/10/17/learning-tex/learning-tex.tex)
