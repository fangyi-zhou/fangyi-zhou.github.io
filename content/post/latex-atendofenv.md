---
math: true
title: Meta-Programming in LaTeX
draft: true
---

Many researchers write paper in $\LaTeX$ because it provides a convenient macro
system that makes typesetting maths equations easy (or difficult, when it
doesn't work).

One way to write papers in $\LaTeX$ is to use as few "advanced features" as
possible, for a vanilla experience: no fancy packages, just use the minimal
setup.
Gradually, as the paper starts to grow long, you begin to think of adding some
macros for things that are too long to type, for example:

```tex
\newcommand{\lCalc}{$\lambda$-calculus}
\newcommand{\pCalc}{$\pi$-calculus}
\newcommand{\myTool}{\textsc{MyTool}}
```

Using macros can help improve consistency in writing, and hopefully reduce a
few keystrokes.
However, some macros may be very similar, like the following:

```tex
\newcommand{\redx}{{\color{red} x}}
\newcommand{\redy}{{\color{red} y}}
\newcommand{\redz}{{\color{red} z}}
```

When you realise red is maybe no longer your favourite colour, and decide
to change everything into blue.
You may think about doing some refactor like this to make future changes
easier:

```tex
\newcommand{\termcolour}{\color{red}}
\newcommand{\termx}{{\termcolour x}}
\newcommand{\termy}{{\termcolour y}}
\newcommand{\termz}{{\termcolour z}}
```

Then you may want to do the same for other syntactic categories, e.g. types,
type variables, ... and okay, maybe some automation is needed.

## Meta-Programming?

The term meta-programming can be interpreted in many ways.
In this context, we want to write a command (i.e. a macro) to generate other
comamnds (i.e. macros).
It is analogous to template programming in C++ or macro programming in C.
Fortunately, $\LaTeX$ is perfectly capable of allowing users to make macros to
create other macros, because EVERYTHING IS A MACRO [^1].

[^1]: Maybe a bit exaggerating.

So intuitively, we want to have something like:

```tex
\makeMacro{term}{x}
```

to expand into
```tex
\newcommand{\termx}{{\termcolour x}}
```
