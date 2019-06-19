---
title: |
  boost-histogram and hist
author: Henry Schreiner
date: \today
fontsize: 10pt
aspectratio: 169
header-includes: |
    \usetikzlibrary{decorations.pathreplacing}
---


# Histograms in Python

## Current state of histograms in Python

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

## What is needed?

::: columns
::: column

### Design

* A histogram should be an object
* Manipulation and plotting should be easy


### Performance
 
 * Fast single threaded filling
 * Multithreaded filling (since it's 2019)

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

## Future of histograms in Python

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

# Boost.Histogram (C++14)

## Intro to Boost.Histogram

* Multidimensional templated header-only histogram library: \github{boostorg/histogram}
* Designed by Hans Dembinski, inspired by ROOT, GSL, and histbook

::: columns
::: {.column width=25%}

### Histogram
* Axes
* Storages
* Accumulators

### Axes types
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

% TODO: Fix bh.axis.regular_log(10,1,5).bins()
\end{tikzpicture}
\end{center}
\vspace{-.1cm}

\uncover<2>{\color{presHighlight} \bfseries Boost 1.70 now released with Boost.Histogram!}
\vspace{.1cm}

:::
:::

# boost-histogram (Python)


## Intro to the Python bindings

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


## Design
* 260+ unit tests run on Azure on Linux, macOS, and Windows
* Up to 16 axes supported (may go up or down)

Resembles the original [Boost.Histogram](https://www.boost.org/doc/libs/1_70_0/libs/histogram/doc/html/index.html) where possible, with
changes where needed for Python performance and idioms.

\vspace{.2cm}

::: columns
::: {.column width=48%}

**C++**

```C++
#include <boost/histogram.hpp>
namespace bh = boost::histogram;

auto hist = bh::make_histogram(
  bh::axis::regular<>{2, 0, 1, "x"},
  bh::axis::regular<>{4, 0, 1, "y"});

hist(.2, .3);
hist(.4, .5);
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
```

:::
:::



## Design: Manipulations


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

```pyhton
hist.sum()
```

\vspace{.3cm}

**Access** an axis

```python
axis0 = hist.axis(0)
axis0.edges() # The edges array
axis0.bin(1) # The bin accessors
```


:::
::: column

**Fill** 2D histogram with values or arrays

```python
hist.fill(x, y)
```

\vspace{.3cm}

**Fill** copies in 4 threads, then merge

```python
hist.fill(x, y, threads=4)
```

\vspace{.3cm}

**Fill** in 4 threads (atomic storage only)

```python
hist.fill(x, y, atomic=4)
```

\vspace{.3cm}

**Convert** to Numpy, 0-copy

```python
hist.view()
```

:::
:::



## Planned: Histogram slicing syntax

See [#35](https://github.com/scikit-hep/boost-histogram/issues/35).

### Access:

```python
v = h[b]                      # Returns bin contents, indexed by bin number
v = h[loc(b)]                        # Returns the bin containing the value
```


### Setting
```python
h[...] = np.ndarray(...)      # Setting with an array or histogram sets the
                              # contents if the sizes match
```

## Planned: Histogram slicing syntax (2)


### Slicing:
```python
h == h[:]                                           # Slice over everything
h2 = h[a:b]                       # Slice of histogram (includes flow bins)
h2 = h[:b]                                  # Leaving out endpoints is okay
h2 = h[loc(v):]                    # Slices can be in data coordinates, too
h2 = h[::project]                                   # Projection operations
h2 = h[::rebin(2)]                        # Modification operations (rebin)
h2 = h[a:b:rebin(2)]                # Modifications can combine with slices
h2 = h[a:b:project]      # Adding endpoints to projection operation removes
                         # under or overflow from the calculation
h2 = h[0:len(h2.axis(0)):project]            # Projection without flow bins
h2 = [a:b, ...]                      # Ellipsis work just like normal numpy
h2 = h[::rebin(loc(width))]                # WIP: This should be considered
```

## Planned: Histogram slicing syntax (3)

### Invalid syntax:

```python
h[v, a:b] # You cannot mix slices and bin contents access (h is an integer)
h[1.0]                            # Floats are not allowed, just like numpy
h[::2]                              # Skipping is not (currently) supported
h[..., None]                          # None == np.newaxis is not supported
```


## Flexibility: Axis

::: columns
::: column

* `bh.axis.regular`
    * `bh.axis.regular_uoflow`
    * `bh.axis.regular_noflow`
    * `bh.axis.regular_growth`
* `bh.axis.circular`
* `bh.axis.regular_log`
* `bh.axis.regular_sqrt`
* `bh.axis.regular_pow`
* `bh.axis.integer`
* `bh.axis.integer_noflow`
* `bh.axis.integer_growth`
* `bh.axis.variable`
* `bh.axis.category_int`
* `bh.axis.category_int_growth`

:::
::: column

\vspace{-.3cm}
\begin{tikzpicture}[every path/.style={thick}, scale=.9]
\begin{scope}[xscale=4, yshift=-.1cm]
\draw (0,0) -- (1,0);
\foreach \i in {0, .1, ..., 1.1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (0,.2) [above] {0}; 
\node at (.5,.2) [above] {0.5}; 
\node at (1,.2) [above] {1};
\node at (.5,0) [below] {\verb|bh.axis.regular(10,0,1)|};
\end{scope}

\begin{scope}[yshift=-2.3cm, xshift=2cm]
\draw (0,0) circle (.75);
\foreach \i in {0, 45, ..., 360} {
    \draw (\i:.75) -- (\i:.95);
}
\node at (0:.95) [right]  {$\pi/2$}; 
\node at (90:.95) [above] {0, $2\pi$};
\node at (180:.95) [left] {$\pi$}; 
\node at (270:.95) [below] {$3\pi/3$}; 
\node at (0,-1.3) [below] {\verb|bh.axis.circular(8,0,2*np.pi)|};
\end{scope}

\begin{scope}[xscale=4, yshift=-5.1cm]
\draw (0,0) -- (1,0);
\foreach \i in {0, .3, .5, 1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (0,.2) [above] {0}; 
\node at (.3,.2) [above] {0.3}; 
\node at (.5,.2) [above] {0.5}; 
\node at (1,.2) [above] {1};
\node at (.5,0) [below] {\verb|bh.axis.variable([0,.3,.5,1])|};
\end{scope}

\begin{scope}[xscale=4, yshift=-6.3cm]
\draw (0,0) -- (1,0);
\foreach \i in {0, .2, ..., 1} {
    \draw (\i,0) -- (\i,.2);
}
\foreach \i in {0,...,4} {
    \node at (\i/5 + .1, 0) [above] {\i};
}
\node at (.5,0) [below] {\verb|bh.axis.integer(0,5)|};
\end{scope}

\begin{scope}[xscale=4, yshift=-7.5cm]
\draw (0,0) -- (1,0);
\foreach \i in {0, .2, ..., 1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (.1, 0) [above] {2};
\node at (.3, 0) [above] {5};
\node at (.5, 0) [above] {8};
\node at (.7, 0) [above] {3};
\node at (.9, 0) [above] {7};
\node at (.5,0) [below] {\verb|bh.axis.category_int([2,5,8,3,7])|};
\end{scope}


\end{tikzpicture}

:::
:::

## Flexibility: Storage types

* `bh.storage.int`
* `bh.storage.double`
* `bh.storage.unlimited` (WIP)
* `bh.storage.atomic_int`
* `bh.storage.weight` (WIP)
* `bh.storage.profile` (WIP, needs sampled fill)
* `bh.storage.weighted_profile` (WIP, needs sampled fill)


## Performance

The following classic measurements are with:

::: columns
::: column

### 1D
* 100 regular bins
* 10,000,000 entries

:::
::: column

### 2D
* 100x100 regular bins
* 1,000,000 entries

:::
:::


See my [histogram performance post](https://iscinumpy.gitlab.io/post/histogram-speeds-in-python/) for measurements of other libraries.

## Performance: macOS, dual core, 1D

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |  149.4 ms |      1x |
|  Any  |   int   |      |    236 ms |   0.63x |
|Regular|   int   |      |  86.23 ms |    1.7x |
|Regular|  aint   |  1   |    132 ms |    1.1x |
|Regular|  aint   |  2   |  168.2 ms |   0.89x |
|Regular|  aint   |  4   |  143.6 ms |      1x |
|Regular|   int   |  1   |  84.75 ms |    1.8x |
|Regular|   int   |  2   |   51.6 ms |    2.9x |
|Regular|   int   |  4   |  42.39 ms |    3.5x |

## Performance: CentOS7, 24 core, 1D (anaconda)

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |    121 ms |      1x |
|  Any  |   int   |      |  261.5 ms |   0.46x |
|Regular|   int   |      |  142.2 ms |   0.85x |
|Regular|  aint   |  1   |  319.1 ms |   0.38x |
|Regular|  aint   |  48  |  272.9 ms |   0.44x |
|Regular|   int   |  1   |  243.4 ms |    0.5x |
|Regular|   int   |  6   |  94.76 ms |    1.3x |
|Regular|   int   |  12  |  71.38 ms |    1.7x |
|Regular|   int   |  24  |  52.26 ms |    2.3x |
|Regular|   int   |  48  |  43.01 ms |    2.8x |

## Performance: KNL, 64 core, 1D (anaconda)

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |  716.9 ms |      1x |
|  Any  |   int   |      |   1418 ms |   0.51x |
|Regular|   int   |      |    824 ms |   0.87x |
|Regular|  aint   |  1   |  871.7 ms |   0.82x |
|Regular|  aint   |  4   |  437.1 ms |    1.6x |
|Regular|  aint   |  64  |  198.8 ms |    3.6x |
|Regular|  aint   | 128  |  186.8 ms |    3.8x |
|Regular|  aint   | 256  |  195.2 ms |    3.7x |
|Regular|   int   |  1   |  796.9 ms |    0.9x |
|Regular|   int   |  2   |  430.6 ms |    1.7x |
|Regular|   int   |  4   |  247.6 ms |    2.9x |
|Regular|   int   |  64  |  88.77 ms |    8.1x |
|Regular|   int   | 128  |  98.08 ms |    7.3x |
|Regular|   int   | 256  |  112.2 ms |    6.4x |

## Performance: macOS, dual core, 2D

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |  121.1 ms |      1x |
|  Any  |   int   |      |  37.12 ms |    3.3x |
|Regular|   int   |      |   18.5 ms |    6.5x |
|Regular|  aint   |  1   |  20.21 ms |      6x |
|Regular|  aint   |  2   |  14.17 ms |    8.5x |
|Regular|  aint   |  4   |  10.23 ms |     12x |
|Regular|   int   |  1   |  17.86 ms |    6.8x |
|Regular|   int   |  2   |   9.41 ms |     13x |
|Regular|   int   |  4   |  6.854 ms |     18x |

## Performance: CentOS7, 24 core, 2D (anaconda)

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |  87.27 ms |      1x |
|  Any  |   int   |      |  41.42 ms |    2.1x |
|Regular|   int   |      |  21.67 ms |      4x |
|Regular|  aint   |  1   |  38.61 ms |    2.3x |
|Regular|  aint   |  6   |  19.89 ms |    4.4x |
|Regular|  aint   |  24  |  9.556 ms |    9.1x |
|Regular|  aint   |  48  |  8.518 ms |     10x |
|Regular|   int   |  1   |   36.5 ms |    2.4x |
|Regular|   int   |  6   |  8.976 ms |    9.7x |
|Regular|   int   |  12  |  5.318 ms |     16x |
|Regular|   int   |  24  |  4.388 ms |     20x |
|Regular|   int   |  48  |  5.839 ms |     15x |

## Performance: KNL, 64 core, 2D (anaconda)

| Type  | Storage | Fill |    Time   | Speedup |
|-------|---------|------|-----------|---------|
| Numpy | uint64  |      |  439.5 ms |      1x |
|  Any  |   int   |      |  250.6 ms |    1.8x |
|Regular|   int   |      |  135.6 ms |    3.2x |
|Regular|  aint   |  1   |  142.2 ms |    3.1x |
|Regular|  aint   |  4   |  52.71 ms |    8.3x |
|Regular|  aint   |  32  |  12.05 ms |     36x |
|Regular|  aint   |  64  |   16.5 ms |     27x |
|Regular|  aint   | 256  |  43.93 ms |     10x |
|Regular|   int   |  1   |  141.1 ms |    3.1x |
|Regular|   int   |  2   |  70.78 ms |    6.2x |
|Regular|   int   |  4   |  36.11 ms |     12x |
|Regular|   int   |  64  |  18.93 ms |     23x |
|Regular|   int   | 128  |  36.09 ms |     12x |
|Regular|   int   | 256  |  55.64 ms |    7.9x |

## Performance: Summary

| System       | 1D max speedup | 2D max speedup |
|--------------|----------------|----------------|
| macOS 1 core | 1.7 x          | 6.5 x          |
| macOS 2 core | 3.5 x          | 18 x           |
| Linux 1 core | 0.85 x         | 4 x            |
| Linux 24 core| 2.8 x          | 20 x           |
| KNL 1 core   | 0.87 x         | 3.2 x          |
| KNL 64 core  | 8.1 x          | 36 x           |

* Note that Numpy 1D is well optimized (last few versions)
* Anaconda versions may provide a few more optimizations to Numpy
* Mixing axes types in boost-histogram can reduce performance by 2-3x

## Distribution

* We *must* provide excellent distribution.
    * If anyone writes `pip install boost-histogram` and it fails, we have failed.
* Docker ManyLinux1 GCC 9: \github{scikit-hep/manylinuxgcc}
* Used in \github{scikit-hep/iMinuit}, see \github{scikit-hep/azure-wheel-helpers}

::: columns
::: column

### Wheels
* manylinux1 32 and 64 bit, Py 2.7 & 3.5+
* manylinux2010 64 bit, Py 2.7 & 3.5+
* macOS 10.9+ 64 bit, Py 2.7 & 3.6+
* Windows 32 and 64 bit, Py 2.7 & 3.6+

:::
::: column

### Source
* SDist
* Build directly from GitHub

### Conda
* conda-forge package planned when released

:::
:::


```bash
python -m pip install \
    git+https://github.com/scikit-hep/boost-histogram.git@develop
```

\vspace{.3cm}

## Plans

- Add `from_numpy` and numpy style shortcut(s)
- Filling improvements (speed, flexibility)
- Support edges/centers matrices and direct setting
- Add reduce operations
- Release to PyPI
- Add [more docs](https://boost-histogram.readthedocs.io/en/latest/)

## Bikeshedding (API discussion)


::: columns
::: {.column width=60%}

Let's discuss API! (On [GitHub issues](https://github.com/scikit-hep/boost-histogram/issues) or [gitter](https://gitter.im/HSF/PyHEP-histogramming))

\vspace{.1cm}

* Download: `pip install boost-histogram` (WIP)
* Use: `import boost.histogram as bh`
* Create: `hist = bh.histogram(bh.axis.regular(12,0,1))`
* Fill: `hist.fill(values)`
* Access values, convert to numpy, etc.

:::
::: {.column width=40%}

\includegraphics[width=\textwidth, trim=0 8.5in 5.2in 0, clip]{Bikeshedding.pdf}

:::
:::

### Documentation
* The documentation will also need useful examples, feel free to contribute!

## Progress

See [#18](https://github.com/scikit-hep/boost-histogram/issues/18)

# hist

## A slide about hist

`hist` is the 'wrapper' piece that does plotting and interacts with the rest of the ecosystem.

::: columns
::: {.column width=60%}

### Plans

* Easy plotting adaptors (mpl-hep)
* Serialization formats (ROOT, HDF5)
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


## Support
* Supported by [IRIS-HEP](http://iris-hep.org), [NSF OAC-1836650](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1836650)
