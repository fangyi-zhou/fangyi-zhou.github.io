---
title: Producing long and short versions of a paper in LaTeX
date: 2023-02-27
---

Conferences have different submission requirements for papers.
Submissions are usually subject to a page limit, but supplementary materials
(or appendices) are permitted.
Here is a guide to produce various versions of a paper in LaTeX.

## Short and Long Versions

The easiest way is probably to produce a short version that satisfies the page
limit,
and a long version containing omitted details (usually in an appendix).
This way has the benefit that one can simply read the long version, as it
usually contains the short version in its entirely.

To achieve this (with a single `.tex` file), we use the package
[etoolbox](https://ctan.org/pkg/etoolbox),
which provides ways to perform conditional compilation.

First, load the package:

```tex
\usepackage{etoolbox}
```

Then, define a new "toggle" (i.e. a boolean switch) called `full`:

```tex
\newtoggle{full}
```

After that, you can do some conditional compilation using the command
`\iftoggle`, for example:

```tex
\iftoggle{full}{%
    See \cref{sec:proof} for the proof.
}{%
    See the full version for the proof.
}
```

## Main and Appendix Separately

Rarely, the main document and the appendix needs to be submitted separately
(e.g. when the paper is not allowed to contain an appendix).
While one can create a long version of the paper,
and use an external program to split the PDF into two parts,
this has the disadvantage that hyperlinks are likely to be broken.

To achieve this, we use two `.tex` files,
one called `main.tex` and one called `supplement.tex`,
and utilise the package [xr-hyper](https://ctan.org/pkg/xr-hyper) for
cross-file cross-references and hyperlinks.

IMPORTANT: You must load the package `xr-hyper` before loading `hyperref`:

```tex
\usepackage{xr-hyper}
\usepackage{hyperref}
```

In `main.tex`, you can use the following command to load the references in
`supplement.tex`:

```tex
\externaldocument{supplement}
```

If you want all labels in `supplement.tex` to be prefixed, e.g. to prevent
duplicate labels, then you can add the prefix `S-` in an optional argument:

```tex
\externaldocument[S-]{supplement}
```

Similarly, in `supplement.tex`, load the references defined in `main.tex`:

```tex
\externaldocument{main}
```

When compiling your documents, you would first need to compile `main.tex` a few
times, and then compile `supplement.tex` a few times, and then compile
`main.tex` again (so that it can use the references defined in `supplement.tex`).

You can use the your favourite references commands as usual, and expect them to
work (although cross-file hyperlink would not work).

If you use `latexmk`, you may need to add additional rules in `latexmkrc` file,
or use the technique in this [Overleaf guide][overleaf-guide].

## Numbering Figures/Tables Continuously

If you have figures in the supplement file, and you wish to continue the
numbering of figures from the main file.
You can always reset the figure counter by hand, but you'd need to change the
counter when you add and remove figures.

At the end of the main file, add the following lines:

```tex
\makeatletter
\protected@write\@auxout{}{\string\newlabel{lastfigure}{{\thefigure}{\thepage}{}{}{}}}
\protected@write\@auxout{}{\string\newlabel{lasttable}{{\thetable}{\thepage}{}{}{}}}
```

By doing so, we have defined two labels: `lastfigure` and `lasttable`, which
contains the value of the figure and table counter respectively.

At the beginning of the supplement file, add the following lines, to reset the
counters to the "last" figure and table respectively:

```tex
\setcounterref{figure}{lastfigure}
\setcounterref{table}{lasttable}
```

Note: if you included a prefix for references in the `main.tex` file, you'd
need to add that prefix before `lastfigure` and `lasttable`.

# References

- https://tex.stackexchange.com/questions/41539/does-hyperref-work-between-two-files
- https://www.overleaf.com/learn/how-to/Cross_referencing_with_the_xr_package_in_Overleaf

[overleaf-guide]: https://www.overleaf.com/learn/how-to/Cross_referencing_with_the_xr_package_in_Overleaf
