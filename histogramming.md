---
title: |
  Histogramming
author: Henry Schreiner
date: \today
fontsize: 10pt
aspectratio: 1610
header-includes: |
    \usetikzlibrary{decorations.pathreplacing}
---


# Overview

* Part 1: Overview of histograms
    * Components of a Histogram
    * Histograms in Python
    * Boost.Histogram in C++14
    * Introducing: `boost-histogram` for Python
    * Outlook, with `hist` and `aghast`
* Part 2: Hands-on with `boost-histogram`


# What is a histogram?

* A histogram is a set of accumulators over data in ranges
    * Usually continuous in Physics, could also be categories
    * Accumulators often are a sum of values - can contain other components

* Input values are digitized by axes (AKA binnings)
    * Categories
    * Real values
        * Variable sized bins (usually give edges)
        * Regular binning (#bins, start, stop)
    * May have special features (overflow, circular, etc.)


# Histogram components

::: columns
::: {.column width=25%}

A `histogram is a collection of 1+ axes
and an accumulator.

## Performance

* Variable axis - list of edges
is most general but requires a sorted search.
* Regular axis: regular spacing

:::
::: {.column width=70%}

\begin{center}
\begin{tikzpicture}[every path/.style={thick}]

% Main grid
\draw [presDark] (-2,-2) rectangle (2,2);
\foreach \i in {-1,0,1} {
    \draw [presDark] (-2,\i) -- (2,\i);
}
\foreach \i in {-1.50465122, -0.76393202,  0.34370152} {
    \draw [presDark] (\i, -2) -- (\i, 2);
}

% Left axis
\draw [presDark2] (-2.3,-2) -- (-2.3,2);
\foreach \i in {-2,-1,0,1,2} {
    \draw [presDark2] (-2.45,\i) -- (-2.15,\i);
}
\node at (-2.3,2) [above, presDark2] {Regular axis};

% Bottom axis
\draw [presDark2] (-2,-2.3) -- (2,-2.3);
\foreach \i in {-2.        , -1.50465122, -0.76393202,  0.34370152,  2.} {
    \draw [presDark2] (\i, -2.45) -- (\i, -2.15);
}
\node at (2,-2.2) [presDark2, right=.4cm, align=left] {Variable axis};

% Axes
\begin{scope}[xshift=-2.9cm, yshift=-2.3cm, rotate=45]
\draw [presDark2, decorate, decoration={brace,amplitude=10pt}, xshift=-4pt, yshift=0pt]
    (0.5,-0.7) -- (0.5,0.7);
\node at (-.3,0) [presDark2] {axes};
\end{scope}

% Overflow bin
\draw [presLight2] (2.05,-2.3) -- (2.4, -2.3);
\draw [presLight2] (2.4, -2.45) -- (2.4, -2.15);
\draw [presLight2, latex- ] (2.2, -2.3) -- (2.2, -2.6) node [below] {Optional overflow};

% Underflow bin
\draw [presLight2] (-2.05,-2.3) -- (-2.4, -2.3);
\draw [presLight2] (-2.4, -2.45) -- (-2.4, -2.15);
\draw [presLight2, latex- ] (-2.25, -2.3) -- (-1.8, -2.6) node [below] {Optional underflow};

% Accumulators
\node (accum) at (1.2,1.5) [circle, minimum width=.7cm, draw, presLight] {};
\draw [presLight, latex-] (accum) -- (2.25, .5) node [right, align=left] {Accumulator};

\end{tikzpicture}
\end{center}
\vspace{-.1cm}


:::
:::


# Histograms in (classic) PyROOT

* 1D Regular
```python
h = ROOT.TH1D("", "", 10, 0, 1)
h.FillN(len(arr), arr, ROOT.nullptr)
```

* 1D Variable
```python
h = ROOT.TH1D("", "", (1,2,3,4,5,6))
h.FillN(len(arr), arr, ROOT.nullptr)
```

* 2D Regular
```python
h = ROOT.TH2D("", "", 10, 0, 1, 20, 0, 2)
h.FillN(len(arr[0]), *arr, ROOT.nullptr)
```

# Histogram in Numpy

* 1D Regular
```python
bins, edges = np.histogram(arr, bins=10, range=(0,1))
```

* 1D Variable
```python
bins, edges = np.histogram(arr, bins=(1,2,3,4,5,6))
```

* 2D regular
```python
b, e1, e2 = np.histogram2d(x, y, bins=(10,20), range=((0,1),(0,2)))
```

# Numpy Pros and Cons

:::columns
:::column

## Pros
* Comes with Numpy
* Good for interactive operations (auto binning)
* *Reasonably* fast
* Density option, weight support too

:::
:::column

## Cons
* Manipulation of plain arrays
* One time fill
* 2D+ not optimized for regular binning
* 1D, 2D, and ND syntax variations
* MPL had to mimic: `plt.hist`
:::
:::

# PyROOT Pros and Cons

:::columns
:::column

## Pros
* Full histogram object
* Iterative fill option
* Weights option
* Can track sum of weights too
* Has profile histograms
:::
:::column

## Cons
* ROOT requirement (Conda-Forge helps)
* Unpythonic interactive exploration
* Odd syntax, odd memory model
* Max 3D
:::
:::


# Histogram Libraries

::: columns
::: {.column width=30%}

- Narrow focus: speed, plotting, or language
- Many are abandoned
- Often issues with design, backends, distribution
- No/little *interaction*

:::
::: {.column width=70%}

\begin{center}
\begin{tikzpicture}[every node/.style={font=\bfseries\large}]
\pgfmathsetseed{40}
\node at (rand*4,rand*4)  {\huge       \href{https://github.com/scikit-hep/histbook}{\color{presLight}HistBook}};
\node at (rand*4,rand*4)  {\Large      \href{https://histogrammar.org}{\color{presLight}Histogrammar}};
\node at (rand*4,rand*4)  {\normalsize \href{https://pygram11.readthedocs.io}{pygram11}};
\node at (rand*4,rand*4)  {\small      \href{https://github.com/drdavis/rootplotlib}{rootplotlib}};
\node at (rand*4-1,rand*4){\LARGE      \href{https://root.cern.ch/pyroot}{\color{presDark}PyROOT}};
\node at (rand*4,rand*4)  {            \href{https://yoda.hepforge.org}{YODA}};
\node at (rand*4,rand*4)  {\huge       \href{https://physt.readthedocs.io/en/latest/tutorial.html}{\color{presDark}physt}};
\node at (rand*4,rand*4)  {\Large      \href{https://github.com/astrofrog/fast-histogram}{\color{presDark2}fast-histogram}};
\node at (rand*4,rand*4)  {\normalsize \href{https://pypi.org/project/qhist/}{qhist}};
\node at (rand*4,rand*4)  {            \href{https://vaex.io}{Vaex}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/hdrhistogram/}{hdrhistogram}};
\node at (rand*4,rand*4+1){            \href{https://pypi.org/project/multihist/}{multihist}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/matplotlib-hep/}{matplotlib-hep}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/pyhistogram/}{\color{presLight}pyhistogram}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/histogram}{\color{presLight}histogram}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/SimpleHist/}{\color{presLight}SimpleHist}};
\node at (rand*4,rand*4)  {            \href{https://pypi.org/project/paida/}{\color{presLight}paida}};
\node at (rand*4,rand*4)  {            \href{https://github.com/theodoregoetz/histogram}{theodoregoetz}};
\node at (2,2)            {\Huge       \href{https://www.numpy.org/}{\color{presDark}numpy}};
\end{tikzpicture}
\end{center}

:::
:::



# [Physt](https://physt.readthedocs.io)

::: columns
::: column

* Histograms as objects
* Pure Python - Dropped Python 2 this year :)
* Very slow fills (slower than numpy)

\vspace{1em}

```python
hist = histogram(heights)
hist.plot(show_values=True)
```

\vspace{1em}

* Powerful plotting
* Easy conversion to Pandas and many more (ROOT through uproot)
* Special histograms, like polar histograms

:::
::: column

![Physt example default plot](https://physt.readthedocs.io/en/latest/_images/tutorial_9_0.png)

:::
:::


# [Fast-Histogram](https://github.com/astrofrog/fast-histogram)

::: columns
::: column

* Exactly like numpy, but faster
    * C kernel
    * Takes advantage of regular binning
    * Can be 20-25x faster for 2D histograms
    * Missing some features / combinations

:::
::: column
![Fast Histogram 2d comparison with Numpy](https://github.com/astrofrog/fast-histogram/raw/master/speedup_compared.png)
:::
:::


# HistBook (archived)

::: columns
::: {.column width=25%}

![](https://github.com/scikit-hep/histbook/raw/master/docs/source/logo-500px.png)

:::
:::


The first Scikit-HEP library for histograms

* Designed for shared axis histogram collections
* Plotting with Vega-Light

Now deprecated and in archive mode, functionality may return in Hist (see next slides).

::: columns
::: {.column width=60%}

\vspace{2em}

```python
>>> array = np.random.normal(0, 1, 1000000)
>>> histogram = Hist(bin("data", 10, -5, 5))
>>> histogram.fill(data=array)
>>> histogram.step("data").to(canvas)
```
:::
::: {.column width=40%}

![](https://github.com/scikit-hep/histbook/raw/master/docs/source/intro-1.png)

:::
:::


# SciKit-HEP Histogramming plan

* boost-histogram: Fast filling and manipulation (core library)
* hist: Simple analysis frontend
* aghast: Conversions between histogram libraries
* UHI: Unified Histogram Indexing: A way for histograms to be indexed cross-library (boost-histogram and hist to begin with)


\begin{centering}
\begin{tikzpicture}[scale=1.5, every path/.style={presDark, thick}]
\node at (-2.5,0) [left, presDark2] {\vphantom{yH}Core histogramming libraries};
\node (bh) at (-.5,0)   [presDark2]  {\vphantom{yH}boost-histogram};
\node (root) at (2,0)   [presDark2] {\vphantom{yH}ROOT};

\node at (-2.5,-1) [left, presLight] {\vphantom{yH}Universal adaptor};
\node (aghast) at (0,-1) [presLight] {\vphantom{yH}Aghast};

\node at (-2.5,-2)     [left, presLight2] {\vphantom{yH}Front ends (plotting, etc)};
\node (hist) at (-1.5,-2)    [presLight2] {\vphantom{yH}hist};
\node (mplhep) at (-.3,-2)   [presLight2] {\vphantom{yH}mpl-hep};
\node (physt) at (.7,-2)     [presLight2] {\vphantom{yH}physt};
\node (others) at (2,-2)     [presLight2] {\vphantom{yH}others};

\draw [latex-latex] (bh) -- (aghast);
\draw [latex-latex] (root) -- (aghast);
\draw [latex-latex] (mplhep) -- (aghast);
\draw [latex-latex] (hist) -- (aghast);
\draw [latex-latex] (hist) -- (bh);
\draw [latex-latex] (physt) -- (aghast);
\draw [latex-latex] (others) -- (aghast);
\end{tikzpicture}
\end{centering}



# Boost.Histogram C++14

* Multidimensional templated header-only histogram library: \github{boostorg/histogram}
* Designed by Hans Dembinski, inspired by ROOT and GSL

::: columns
::: {.column width=25%}

## Histogram
* Axes
* Storage

## Axes types
* Regular, Circular
* Variable
* Integer
* Category

:::
::: {.column width=70%}

\begin{center}
\begin{tikzpicture}[every path/.style={thick}]

% Main grid
\draw [presDark] (-2,-2) rectangle (2,2);
\foreach \i in {-1,0,1} {
    \draw [presDark] (-2,\i) -- (2,\i);
}
\foreach \i in {-1.50465122, -0.76393202,  0.34370152} {
    \draw [presDark] (\i, -2) -- (\i, 2);
}
\draw [latex-, presDark] (2,2) -- (2.4,2.2) node [right] {Storage $\begin{pmatrix}\textrm{Static}\\ \textrm{Dynamic}\end{pmatrix}$};

% Left axis
\draw [presDark2] (-2.3,-2) -- (-2.3,2);
\foreach \i in {-2,-1,0,1,2} {
    \draw [presDark2] (-2.45,\i) -- (-2.15,\i);
}
\node at (-2.3,2) [above, presDark2] {Regular axis};

% Bottom axis
\draw [presDark2] (-2,-2.3) -- (2,-2.3);
\foreach \i in {-2.        , -1.50465122, -0.76393202,  0.34370152,  2.} {
    \draw [presDark2] (\i, -2.45) -- (\i, -2.15);
}
\node at (2,-2.2) [presDark2, right=.4cm, align=left] {Regular axis with\\log transform};

% Axes
\begin{scope}[xshift=-2.9cm, yshift=-2.3cm, rotate=45]
\draw [presDark2, decorate, decoration={brace,amplitude=10pt}, xshift=-4pt, yshift=0pt]
    (0.5,-0.7) -- (0.5,0.7);
\node at (-.3,0) [presDark2] {axes};
\end{scope}

% Overflow bin
\draw [presLight2] (2.05,-2.3) -- (2.4, -2.3);
\draw [presLight2] (2.4, -2.45) -- (2.4, -2.15);
\draw [presLight2, latex- ] (2.2, -2.3) -- (2.2, -2.6) node [below] {Optional overflow};

% Underflow bin
\draw [presLight2] (-2.05,-2.3) -- (-2.4, -2.3);
\draw [presLight2] (-2.4, -2.45) -- (-2.4, -2.15);
\draw [presLight2, latex- ] (-2.25, -2.3) -- (-1.8, -2.6) node [below] {Optional underflow};

% Accumulators
\node (accum) at (1.2,1.5) [circle, minimum width=.7cm, draw, presLight] {};
\draw [presLight, latex-] (accum) -- (2.25, .5) node [right, align=left] {Accumulator\\int, double,\\unlimited, ...};

\end{tikzpicture}
\end{center}
\vspace{-.1cm}


:::
:::

# Boost.Histogram example

```c++
#include <boost/histogram.hpp>
#include <boost/histogram/ostream.hpp>
#include <random>

int main() {
    namespace bh = boost::histogram;

    auto hist = bh::make_histogram(bh::axis::regular<>{20, -3, 3});

    std::default_random_engine eng;
    std::normal_distribution<double> dist(0, 1);
    for(int n = 0; n < 10'000; ++n)
        hist(dist(eng));

    std::cout << hist << std::endl;
    return 0;
}
```

# Boost.Histogram example (output) {.shrink}

```
histogram(regular(20, -3, 3, options=underflow | overflow))
                  +----------------------------------------------------------+
[-inf,   -3) 9    |                                                          |
[  -3, -2.7) 19   |=                                                         |
[-2.7, -2.4) 36   |==                                                        |
[-2.4, -2.1) 110  |=====                                                     |
[-2.1, -1.8) 191  |=========                                                 |
[-1.8, -1.5) 275  |=============                                             |
[-1.5, -1.2) 518  |=========================                                 |
[-1.2, -0.9) 644  |===============================                           |
[-0.9, -0.6) 914  |============================================              |
[-0.6, -0.3) 1107 |=====================================================     |
[-0.3,    0) 1183 |========================================================= |
[   0,  0.3) 1185 |========================================================= |
[ 0.3,  0.6) 1120 |======================================================    |
[ 0.6,  0.9) 874  |==========================================                |
[ 0.9,  1.2) 663  |================================                          |
[ 1.2,  1.5) 491  |========================                                  |
[ 1.5,  1.8) 322  |===============                                           |
[ 1.8,  2.1) 172  |========                                                  |
[ 2.1,  2.4) 79   |====                                                      |
[ 2.4,  2.7) 38   |==                                                        |
[ 2.7,    3) 28   |=                                                         |
[   3,  inf) 22   |=                                                         |
                  +----------------------------------------------------------+
```

# boost-histogram: Python bindings

::: columns
::: column

### Design

* A histogram should be an object
* Manipulation and plotting should be easy


### Performance
 
* Fast filling
* Compiled composable manipulations

:::
::: column

### Flexibility

* Axes options: sparse, growing, labels
* Storage: integers, weights, errors...

### Distribution

* Easy to use anywhere, pip or conda
* Should have wheels, be easy to build, etc.

:::
:::


# Intro to the Python bindings

* Boost.Histogram developed with Python in mind
* Original bindings based on Boost::Python
    * Hard to build and distribute
    * Somewhat limited
* New bindings: \github{scikit-hep/boost-histogram}
    * 0-dependency build (C++14 only)
    * State-of-the-art PyBind11

\vspace{.5cm}

\begin{center}
\begin{tikzpicture}[scale=1.25]
\node [rectangle, rounded corners, white, fill=presDark, minimum width=2.2cm, minimum height=.75cm] at (0,0) {Design};
\node [rectangle, rounded corners, white, fill=presLight, minimum width=2.2cm, minimum height=.75cm] at (2.5,0) {\vphantom{g}Flexibility};
\node [rectangle, rounded corners, white, fill=presDark2, minimum width=2.2cm, minimum height=.75cm] at (5,0) {Speed};
\node [rectangle, rounded corners, white, fill=presLight2, minimum width=2.2cm, minimum height=.75cm] at (7.5,0) {\vphantom{g}Distribution};
\end{tikzpicture}
\end{center}


# Design
* 500+ unit tests run on Azure on Linux, macOS, and Windows

Resembles the original [Boost.Histogram](https://www.boost.org/doc/libs/1_71_0/libs/histogram/doc/html/index.html) where possible, with
changes where needed for Python performance and idioms.

\vspace{.2cm}

::: columns
::: {.column width=48%}

**C++14**

```C++
#include <boost/histogram.hpp>
namespace bh = boost::histogram;

auto hist = bh::make_histogram(
  bh::axis::regular<>{2, 0, 1, "x"},
  bh::axis::regular<>{4, 0, 1, "y"});

hist(.2, .3); // Fill will also be
hist(.4, .5); // availble in 1.7.2
hist(.3, .2);
```
:::
::: {.column width=55%}

**Python**

```python
import boost.histogram as bh


hist = bh.histogram(
  bh.axis.regular(2, 0, 1, metadata="x"),
  bh.axis.regular(4, 0, 1, metadata="y"))

hist.fill(
    [.2, .4, .3],
    [.3, .5, .2])

:::
:::



# Design: Manipulations


::: columns
::: column

**Combine** two histograms

```python
hist1 + hist2
```

\vspace{.3cm}

**Scale** a histogram

```python
hist * 2.0
```

\vspace{.3cm}

**Sum** a histogram contents

```python
hist.sum()
```

\vspace{.3cm}

**Access** an axis

```python
ax = hist.axes[0]
ax.edges   # The edges array
ax.centers # Centers of bins
ax.widths  # Width of each bin

hist.axes.centers # All centers
# Etc.
```


:::
::: column

**Fill** 2D histogram with values or arrays

```python
hist.fill(x, y)
```

\vspace{.3cm}

**Convert** contents to Numpy array

```python
hist.view()
```

\vspace{.3cm}

**Convert** to Numpy style histogram tuple

```python
hist.to_numpy()
```

\vspace{.3cm}

**Pickle** supported (multiprocessing)

```python
pickle.dumps(hist, -1)
```

\vspace{.3cm}

**Copy/deepcopy** supported

```python
hist2 = copy.deepcopy(hist)
```


:::
:::

# Unified Histogram Indexing (UHI)

The language here (`bh.loc`, etc) is defined in such a way that any library can provide them - "Unified".

\vspace{.3cm}

Access

```python
v = h[b]            # Returns bin contents, indexed by bin number
v = h[bh.loc(b)]    # Returns the bin containing the value
v = h[bh.underflow] # Underflow and overflow can be accessed with special tags
```

\vspace{.3cm}

Setting

```python
h[b] = v
h[bh.loc(b)] = v
h[bh.underflow] = v
```

# Unified Histogram Indexing (UHI) (2)

```python
h == h[:]               # Slice over everything
h2 = h[a:b]             # Slice of histogram (includes flow bins)
h2 = h[:b]              # Leaving out endpoints is okay
h2 = h[bh.loc(v):]      # Slices can be in data coordinates, too
h2 = h[::bh.project]    # Sum an axis (name may change)
h2 = h[::bh.rebin(2)]   # Modification operations (rebin)
h2 = h[a:b:bh.rebin(2)] # Modifications can combine with slices
h2 = h[a:b, ...]        # Ellipsis work just like normal numpy
```

* [Docs are here](https://boost-histogram.readthedocs.io/en/latest/usage/indexing.html)
* Description may move to a new repository

# Performance

* Factor of 2 faster than 1D regular binning in Numpy 1.17
    * Currently no specialization, just a 1D regular fill
    * Could be optimized further
* Factor of 6-10 faster than 2D regular binning Numpy


# Distribution

* We *must* provide excellent distribution.
    * If anyone writes `pip install boost-histogram` and it fails, we have failed.
* Docker ManyLinux1 GCC 9.2: \github{scikit-hep/manylinuxgcc}
* Used in \github{scikit-hep/iMinuit}, see \github{scikit-hep/azure-wheel-helpers}

::: columns
::: column

### Wheels
* manylinux1 32 and 64 bit, Py 2.7 & 3.5--3.7
* manylinux2010 64 bit, Py 2.7 & 3.5--3.8
* macOS 10.9+ 64 bit, Py 2.7 & 3.6--3.8
* Windows 32 and 64 bit, Py 2.7 & 3.6--3.7

:::
::: column

### Source
* SDist
* Build directly from GitHub

### Conda
* conda-forge package planned

:::
:::


```bash
python -m pip install boost-histogram
# OR git+https://github.com/scikit-hep/boost-histogram.git@develop
```

\vspace{.3cm}



# Hist

`hist` is the 'wrapper' piece that does plotting and interacts with the rest of the ecosystem.

::: columns
::: {.column width=60%}

### Plans

* Easy plotting adaptors (mpl-hep)
* Serialization formats via Aghast (ROOT, HDF5)
* Auto-multithreading
* Statistical functions (Like TEfficiency)
* Multihistograms (HistBook)
* Interaction with fitters (ZFit, GooFit, etc)
* Bayesian Blocks algorithm from SciKit-HEP
* Command line histograms for stream of numbers

:::
::: {.column width=35%}

### Call for contributions

* What do you need?
* What do you want?
* What would you like?

###
Join in the development! This should combine the best features of other packages.

:::
:::

# Aghast

::: columns
::: {.column width=25%}

![](https://github.com/scikit-hep/aghast/raw/master/docs/source/logo-300px.png)

:::
::: {.column width=65%}

Aghast is a histogramming library that does not fill histograms and does not plot them.

:::
:::

* A memory format for histograms, like Apache Arrow
* Converts to and from other libraries
* Uses flatbuffers to hold histograms
* Indexing ideas inspired the UHI

## Binnings

IntegerBinning
• RegularBinning
• HexagonalBinning
• EdgesBinning
• IrregularBinning
• CategoryBinning
• SparseRegularBinning
• FractionBinning
• PredicateBinning
• VariationBinning


# End of part 1

Now, we will go hands on with the first beta of boost-histogram!

## Support
* Supported by [IRIS-HEP](http://iris-hep.org), [NSF OAC-1836650](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1836650)


