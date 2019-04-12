---
title: |
  boost-histogram and hist
author: Henry Schreiner
date: \today
fontsize: 10pt
aspectratio: 169
---


# Histogramming in Python

## Current state of Histogramming in Python

::: columns
::: {.column width=30%}

### Core library: numpy
- Historically slow
- No histogram object
- Plotting is separate

### Other libraries
- Narrow focus: speed, plotting, or language
- Many are abandoned
- Poor design, backends, distribution

:::
::: {.column width=70%}

\begin{center}
\begin{tikzpicture}[every node/.style={font=\bfseries\large}]
\pgfmathsetseed{40}
\node at (rand*4,rand*4)  {\huge       \href{https://github.com/scikit-hep/histbook}{HistBook}};
\node at (rand*4,rand*4)  {\Large      \href{https://histogrammar.org}{Histogrammar}};
\node at (rand*4,rand*4)  {\normalsize \href{https://pygram11.readthedocs.io}{pygram11}};
\node at (rand*4,rand*4)  {\small      \href{https://github.com/drdavis/rootplotlib}{rootplotlib}};
\node at (rand*4-1,rand*4){\LARGE      \href{https://root.cern.ch/pyroot}{PyROOT}};
\node at (rand*4,rand*4)  {            YODA};
\node at (rand*4,rand*4)  {\huge       physt};
\node at (rand*4,rand*4)  {\Large      fast-histogram};
\node at (rand*4,rand*4)  {\normalsize qhist};
\node at (rand*4,rand*4)  {            Vaex};
\node at (rand*4,rand*4)  {            hdrhistogram};
\node at (rand*4,rand*4+1){            multihist};
\node at (rand*4,rand*4)  {            matplotlib-hep};
\node at (rand*4,rand*4)  {            pyhistogram};
\node at (rand*4,rand*4)  {            histogram};
\node at (rand*4,rand*4)  {            SimpleHist};
\node at (rand*4,rand*4)  {            paida};
\node at (rand*4,rand*4)  {\normalsize theodoregoetz};
\node at (2,2)            {\Huge       numpy};
\end{tikzpicture}
\end{center}

:::
:::

## What is needed?

::: columns
::: column

### Speed
 
 * Fast single threaded filling
 * Multithreaded filling (since it's 2019)

### Distribution

* Easy to use anywhere, pip or conda
* Should have wheels, be easy to build, etc.

:::
::: column

### Design

* A histogram should be an object
* Manipulation and plotting should be easy

### Power

* Axes options: sparse, growing, labels
* Storage: integers, weights, errors...

:::
:::

## Future of histogramming in Python

\begin{centering}
\begin{tikzpicture}
\node at (-2,0) [left] {Core histogramming libraries};
\node (bh) at (-.5,0) {boost-histogram};
\node (root) at (2,0) {ROOT};

\node at (-2,-1) [left] {Universal adaptor};
\node (aghast) at (0,-1) {Aghast};

\node at (-2,-2) [left] {Front ends (plotting, etc)};
\node (mplhep) at (-1.5, -2) {mpl-hep};
\node (hist) at (-.3,-2) {hist};
\node (physt) at (.7,-2) {physt};
\node (others) at (2,-2) {others};

\draw [latex-latex] (bh) -- (aghast);
\draw [latex-latex] (root) -- (aghast);
\draw [latex-latex] (mplhep) -- (aghast);
\draw [latex-latex] (hist) -- (aghast);
\draw [latex-latex] (physt) -- (aghast);
\draw [latex-latex] (others) -- (aghast);
\end{tikzpicture}
\end{centering}

# Boost::Histogram (C++14)

## Intro to Boost::Histogram

* Multidimensional templated header-only histogram library in boost
* Inspired by ROOT, GSL, and histbook

###
Boost 1.70 released with Boost::Histogram!

# boost-histogram (Python)

## Intro to the Python bindings

* Boost::Histogram developed with Python in mind
* Original bindings based on Boost::Python
    * Hard to build and distribute
    * Somewhat limited
* New bindings developed as part of SciKit-HEP
    * 0-dependency build (C++14 only)
    * State-of-the-art PyBind11

:::columns
::: {.column width=33%}
###
Wheels - manylinux1, manylinux2010, macOS, Windows
:::
::: {.column width=33%}
###
Github build, sdist build
:::
::: {.column width=33%}
###
conda package planned
:::
:::
