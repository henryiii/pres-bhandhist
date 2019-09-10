---
title: |
  `boost-histogram` and `hist` Roadmap
author: Henry Schreiner
date: September 12-13, 2019
fontsize: 10pt
aspectratio: 169
header-includes: |
    \usetikzlibrary{decorations.pathreplacing}
---


# Future of histograms in Python

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



# Intro to Boost.Histogram C++14

* Multidimensional templated header-only histogram library: \github{boostorg/histogram}
* Designed by Hans Dembinski, inspired by ROOT, GSL, and histbook

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

% TODO: Fix bh.axis.regular_log(10,1,5).bins()
\end{tikzpicture}
\end{center}
\vspace{-.1cm}


:::
:::



# boost-histogram (Python)


\github{scikit-hep/boost-histogram}

* 0-dependency build (C++14 only)
* State-of-the-art PyBind11
* 280+ unit tests run on Azure on Linux, macOS, and Windows
* Binary wheels on Azure for all major platforms
- [Read the docs (in progress)](https://boost-histogram.readthedocs.io/en/latest/)

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

Resembles the original [Boost.Histogram](https://www.boost.org/doc/libs/1_70_0/libs/histogram/doc/html/index.html) where possible, with
changes where needed for Python performance and idioms.

\vspace{.2cm}

::: columns
::: {.column width=48%}

**C++**

```C++
#include <boost/histogram.hpp>
namespace bh = boost::histogram;

// The make_ can be dropped in C++17
auto hist = bh::make_histogram(
  bh::axis::regular<>{2, 0, 1, "x"},
  bh::axis::regular<>{4, 0, 1, "y"});

hist(.2, .3); // .fill being added
hist(.4, .5); // in Boost 1.72
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

**Convert** to Numpy, 0-copy if possible

```python
hist.view()
```

:::
:::



# Unified Histogram Indexing

## Access:

```python
v = h[b]                      # Returns bin contents, indexed by bin number
v = h[loc(b)]                        # Returns the bin containing the value
```


## Setting (planned):
```python
h[...] = np.ndarray(...)      # Setting with an array or histogram sets the
                              # contents if the sizes match
h[b] = v                      # Values can be set, too
```

# Unified Histogram Indexing (2)


### Slicing:
```python
h == h[:]                                           # Slice over everything
h2 = h[a:b]                       # Slice of histogram (includes flow bins)
h2 = h[:b]                                  # Leaving out endpoints is okay
h2 = h[loc(v):]                    # Slices can be in data coordinates, too
h2 = h[::project]                                   # Projection operations
h2 = h[::rebin(2)]                        # Modification operations (rebin)
h2 = h[a:b:rebin(2)]                # Modifications can combine with slices
h2 = [a:b, ...]                      # Ellipsis work just like normal numpy
```

### Slicing (planned)
```python
h2 = h[a:b:project]      # Adding endpoints to projection operation removes
                         # under or overflow from the calculation
h2 = h[0:end:project]           # Projection without flow bins, special tag
```



# Flexibility: 22 axis types

::: columns
::: {.column width=60%}

\vspace{3em}

* `regular`
    - `uoflow`, `uflow`, `oflow`, `noflow`, `growth`
* `regular_ +`
    - `log`, `sqrt`, `pow`
* `circular`
* `integer`
    - `uoflow`, `uflow`, `oflow`, `noflow`, `growth`
* `variable`
    - `uoflow`, `uflow`, `oflow`, `noflow`
* `category`
    - `int` or `str`, `growth`

:::
::: {.column width=40%}

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
\node at (.5,0) [below] {\verb|bh.axis.category[2,5,8,3,7])|};
\end{scope}


\end{tikzpicture}

:::
:::

# Flexibility: 7 storage types

* `bh.storage.int`
* `bh.storage.double`
* `bh.storage.unlimited`
* `bh.storage.atomic_int`
* `bh.storage.weight`
* `bh.storage.profile`
* `bh.storage.weighted_profile`


# Plans (See [#18](https://github.com/scikit-hep/boost-histogram/issues/18))

- Finish UHI implementation
- Add non-double fill
- Clean up some bugs/missing functionality with access
- Add `from_numpy` and numpy style shortcut(s)
- Release to PyPI

## Release
* Becoming stable enougth for internal use in AS!
* Planned release before PyHEP in mid October

# Bikeshedding (API discussion)


::: columns
::: {.column width=60%}

Let's discuss API! (On [GitHub issues](https://github.com/scikit-hep/boost-histogram/issues) or [gitter](https://gitter.im/HSF/PyHEP-histogramming))

\vspace{.1cm}

* Download: `pip install boost-histogram` (Release before PyHEP)
* Use: `import boost.histogram as bh`
* Create: `hist = bh.histogram(bh.axis.regular(12,0,1))`
* Access values, convert to numpy, etc.

:::
::: {.column width=40%}

\includegraphics[width=\textwidth, trim=0 8.5in 5.2in 0, clip]{images/Bikeshedding.pdf}

:::
:::

## Documentation
* The documentation will also need useful examples, feel free to contribute!



# A slide about hist

`hist` is the 'wrapper' piece that does plotting and interacts with the rest of the ecosystem.

::: columns
::: {.column width=60%}

## Plans

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

## Call for contributions

* What do you need?
* What do you want?
* What would you like?

##
Join in the development! This should combine the best features of other packages.

:::
:::

# Discussion

These are just a few question to facilitate discussion.

* Do you plan to use boost-histogram and or hist as part of another package?
* Do you have any unusual histogramming needs?
* What part of `boost-histogram` sounds most useful/exciting?
* Does the boost-histogram API look reasonable?
* What are the analysis/plotting features you need in Hist?


## Support
* Supported by [IRIS-HEP](http://iris-hep.org), [NSF OAC-1836650](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1836650)
