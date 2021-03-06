---
title: |
  Deep Dive
author: Henry Schreiner
date: September 19, 2019
fontsize: 10pt
aspectratio: 1610
header-includes: |
    \usetikzlibrary{decorations.pathreplacing}
---

# Deep Dive

## About me

::: columns
::: column

\begin{center}
\begin{tikzpicture}
\clip (0,0) circle (1.2cm) node {\includegraphics[width=2.4cm]{images/avatar.jpg}};
\end{tikzpicture}

\textbf{Henry Schreiner}

Computational Physicist /\\
Research Software Engineer

\emph{Princeton University}
\end{center}

\vspace{1.3em}

* University of Texas at Austin
    - MayaMuon experiment - cosmic ray measurement, Belize pyramids
* University of Cincinnati
    * LHCb - Fast fitting in charm

::: 
::: column

* Author of [iscinumpy.gitlab.io](https://iscinumpy.gitlab.io)
* Redesigned [scikit-hep.org](http://scikit-hep.org)
* Desired hobby: Computer graphics/modeling
* Actual hobby: Raising a 3-year old

\begin{center}
\includegraphics[width=1.22\textwidth, trim=200 0 0 0]{images/UTFastTimesFigure3.png}
\end{center}

:::
:::


# IRIS-HEP AS: Histograms

## Histogram libraries (Python)


\begin{center}
\begin{tikzpicture}[every node/.style={font=\bfseries\large}]
\pgfmathsetseed{40}
\node at (rand*6,rand*4)  {\huge       \href{https://github.com/scikit-hep/histbook}{\color{presLight}HistBook}};
\node at (rand*6,rand*4)  {\Large      \href{https://histogrammar.org}{\color{presLight}Histogrammar}};
\node at (rand*6,rand*4)  {\normalsize \href{https://pygram11.readthedocs.io}{pygram11}};
\node at (rand*6,rand*4)  {\small      \href{https://github.com/drdavis/rootplotlib}{rootplotlib}};
\node at (rand*6-1,rand*4){\LARGE      \href{https://root.cern.ch/pyroot}{\color{presDark}PyROOT}};
\node at (rand*6,rand*4)  {            \href{https://yoda.hepforge.org}{YODA}};
\node at (rand*6,rand*4)  {\huge       \href{https://physt.readthedocs.io/en/latest/tutorial.html}{\color{presDark}physt}};
\node at (rand*6,rand*4)  {\Large      \href{https://github.com/astrofrog/fast-histogram}{\color{presDark2}fast-histogram}};
\node at (rand*6,rand*4+.2) {\normalsize \href{https://pypi.org/project/qhist/}{qhist}};
\node at (rand*6,rand*4)  {            \href{https://vaex.io}{Vaex}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/hdrhistogram/}{hdrhistogram}};
\node at (rand*6,rand*4+1){            \href{https://pypi.org/project/multihist/}{multihist}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/matplotlib-hep/}{matplotlib-hep}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/pyhistogram/}{\color{presLight}pyhistogram}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/histogram}{\color{presLight}histogram}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/SimpleHist/}{\color{presLight}SimpleHist}};
\node at (rand*6,rand*4)  {            \href{https://pypi.org/project/paida/}{\color{presLight}paida}};
\node at (rand*6,rand*4)  {            \href{https://github.com/theodoregoetz/histogram}{theodoregoetz}};
\node at (2,2.25)         {\Huge       \href{https://www.numpy.org/}{\color{presDark}numpy}};
\node at (rand*6-1.5, rand*4) {\small  \href{https://github.com/opendatagroup/cassius}{\color{presLight2}Cassius}};
\node at (rand*6+3, rand*4) {\tiny     \href{http://code.google.com/p/svgfig}{\color{presLight2}SVGFig}};
\node at (rand*6, rand*4) {\tiny       \href{http://code.google.com/p/plothon}{\color{presLight2}Plothon}};
\node at (1.8, -2)        {\Large      \href{https://coffeateam.github.io/coffea/notebooks/histograms.html}{\color{presDark2}coffea}};

\onslide<2->{
\node at (-4,-1.8) [white, fill=presDark2, rounded corners, inner sep=.3cm] {\large Narrow focus}; 
\node at (-4,-2.8) [white, fill=presDark2, rounded corners, inner sep=.3cm] {\large Most abandoned}; 
}
\onslide<3->{
\begin{scope}[xshift=0.35cm, yshift=1.1cm, presHighlight]
\draw [-latex, line width=.2cm] (.5,0) -- +(15:2.5);
\draw [-latex, line width=.2cm] (-.1,0) -- +(115:2.5);
\draw [-latex, line width=.2cm] (-.5,0) -- +(225:2.5);
\draw [-latex, line width=.2cm] (.5,0) -- +(345:2.5);
\node at (0,0) [white, fill=presHighlight, rounded corners, inner sep=.3cm, align=center] {\large No or little\\ \LARGE\textbf{interaction}}; 
\end{scope}
}

\end{tikzpicture}
\end{center}


## Scikit-HEP histogramming plan (Python)

\begin{tikzpicture}
\path [use as bounding box] (0,0) rectangle (1,.1);
\node at (13.3,-.3) {\includegraphics[width=4cm]{images/ScikitHEPLogo.pdf}};
\end{tikzpicture}

* **boost-histogram**: Fast filling and manipulation (core library)
* ***hist***: Simple analysis frontend
* **aghast**: Conversions between histogram libraries
* **UHI** (Unified Histogram Indexing): Cross-library indexing proposal

\vspace{.35cm}

\begin{centering}
\begin{tikzpicture}[xscale=1.5, yscale=1.4, every path/.style={font=\Large\vphantom{yH}}]

\begin{scope}[presDark]
\node at (-2.5,0) [left] {Core histogramming libraries};
\node (bh) at (-.5,0)    {\bf boost-histogram};
\node (root) at (2,0)    {ROOT};
\end{scope}

\begin{scope}[yshift=-1cm, presLight]
\node at (-2.5,0) [left] {Universal adaptor};
\node (aghast) at (0,0)  {\bf aghast};
\end{scope}

\begin{scope}[yshift=-2cm, presLight2]
\node at (-2.5,0)     [left] {Front ends (plotting, etc)};
\node (hist) at (-1.6,0)     {\textbf{\textit{hist}}};
\node (mplhep) at (-.2,0)    {\textit{mpl-hep}};
\node (physt) at (1.1,0)     {coffea};
\node (others) at (2.4,0)    {others};
\end{scope}

\begin{scope}[ultra thick, presDark2, latex-latex]
\draw (bh) -- (aghast);
\draw (root) -- (aghast);
\draw (mplhep) -- (aghast);
\draw (hist) -- (aghast);
\draw (hist) -- (bh);
\draw (physt) -- (aghast);
\draw (others) -- (aghast);
\end{scope}
\end{tikzpicture}
\end{centering}


## Boost.Histogram C++14

\vspace{-.9cm}
\begin{center}
\includegraphics[width=.4\pagewidth]{images/BoostHistogramLogo.pdf}
\end{center}
\vspace{-.2cm}

::: columns
::: {.column width=35%}

* \github{boostorg/histogram}
* Designed by Hans Dembinski

:::
::: {.column width=50%}

* Header only, with header only Boost
* Heavily unit-tested: 100% line coverage

:::
:::

::: columns
::: {.column width=65%}

### Features
* Static / Dynamic storage (can avoid allocation!)
* Static fill becomes 57 lines of vectorized assembly
* Supports platforms without exceptions or RTTI
* Adding, scaling, slicing, rebinning, projections...
* High performance filling and bin iteration
* Unique memory-efficient dynamic storage

:::
::: {.column width=30%}

### Customizable:
* storage
* allocator
* accumulator
* axes
* axis metadata
* axis transform

:::
:::


\begin{tikzpicture}[overlay, remember picture, every node/.style={
    fill=presHighlight, rounded corners=.3cm, rotate=25, text=white}]

\onslide<2>{
  \node at (current page.center) [inner sep=.4cm, font=\LARGE\vphantom{yH}]
    {Accepted in Boost 1.70!};
}

\onslide<3>{
  \node at (current page.center) [inner sep=.7cm, font=\Huge\bf\vphantom{yH}]
    {Will not go away!};
}

\end{tikzpicture}


## Boost.Histogram C++14: Histogram concept

::: columns
::: {.column width=25%}

\vspace{-.4cm}

### Histogram
* Axes
* Storage

### Axis types
* Regular (circular)
* Variable (circular)
* Integer (circular)
* Category

### Accumulators
* Sum
* Mean

:::
::: {.column width=70%}



Single class replaces `TH1T`, `TH2T`, `TH3T`, `THnT`, `TProfile`, `TProfile2D`, `TProfile3D` and is more general!

\vspace{-.4cm}

\begin{center}
\begin{tikzpicture}[scale=1.2, every path/.style={thick}]

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
## Boost.Histogram C++14: Elegant and powerful

```c++
##include <boost/histogram.hpp>
##include <boost/histogram/ostream.hpp>
##include <random>
##include <iostream>

int main() {
    namespace bh = boost::histogram;

    auto hist = bh::histogram(bh::axis::regular{20, -3, 3}); // C++17 version

    std::default_random_engine eng;
    std::normal_distribution<double> dist{0, 1};
    for(int n = 0; n < 10'000; ++n)
        hist(dist(eng));

    std::cout << hist << std::endl;
    return 0; }
```
\vspace{.1cm}

## Boost.Histogram C++14: Example output {.shrink}

\footnotesize

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


## Boost.Histogram C++14: Performance

::: columns
::: {.column width=110%}
\includegraphics[width=\textwidth]{images/fill_performance.pdf}
:::
:::

\vspace{.6cm}

::: columns
::: column

* `call` is one at a time fill
* `fill` is new (1.72) array fill

:::
::: column

* `S` is static (fixed types)
* `D` is dynamic (unlimited storage)

:::
:::

## boost-histogram: Python bindings

\begin{center}
\includegraphics[width=.4\textwidth]{images/BoostHistogramPythonLogo.pdf}
\end{center}
\vspace{-.2cm}


* Boost.Histogram developed with Python in mind
    * Original prototype bindings based on Boost::Python
* New bindings: \github{scikit-hep/boost-histogram}
    * 0-dependency build (C++14 only)
    * State-of-the-art PyBind11
    * Designed with the original author, Hans Dembinski
* Public beta 0.5.2 currently available, 0.6 coming soon

\vspace{.25cm}

\begin{center}
\begin{tikzpicture}[
  scale=1.25, rectangle, rounded corners, text=white,
  minimum width=2.3cm, minimum height=.75cm, font=\large\vphantom{gH}]
\node [fill=presDark] at (0,0) {Design};
\node [fill=presLight] at (2.5,0) {Flexibility};
\node [fill=presDark2] at (5,0) {Speed};
\node [fill=presLight2] at (7.5,0) {Distribution};
\end{tikzpicture}
\end{center}


## Design
* 500+ unit tests run on Azure on Linux, macOS, and Windows
* Stays close to [Boost.Histogram](https://www.boost.org/doc/libs/1_71_0/libs/histogram/doc/html/index.html) with Pythonizations

\vspace{.2cm}

::: columns
::: {.column width=48%}

**C++17**

```C++
##include <boost/histogram.hpp>
namespace bh = boost::histogram;

auto hist = bh::histogram(
  bh::axis::regular{2, 0, 1, "x"},
  bh::axis::regular{4, 0, 1, "y"});

hist(.2, .3); // Fill will also be
hist(.4, .5); // available in 1.72
hist(.3, .2);
```
:::
::: {.column width=55%}

**Python**

```python
import boost_histogram as bh


hist = bh.Histogram(
  bh.axis.Regular(2, 0, 1, metadata="x"),
  bh.axis.Regular(4, 0, 1, metadata="y"))

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
## Etc.
```


:::
::: column

**Fill** 2D histogram with values or arrays

```python
hist.fill(a1, a2, weights=[w1, w2],
                  samples=[s1, s2])
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
hist2 = hist.copy()
```

\vspace{.3cm}
**Numpy** functions provided

```python
H, E = bh.numpy.histogram(...)
```



:::
:::

## Design: Unified Histogram Indexing (UHI)

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

## Design: Unified Histogram Indexing (UHI) (2)

```python
h == h[:]               # Slice over everything
h2 = h[a:b]             # Slice of histogram (includes flow bins)
h2 = h[:b]              # Leaving out endpoints is okay
h2 = h[bh.loc(v):]      # Slices can be in data coordinates, too
h2 = h[::bh.sum]        # Remove an axis by summation
h2 = h[0:5:bh.sum]      # Can add endpoints
h2 = h[::bh.rebin(2)]   # Modification operations (rebin)
h2 = h[a:b:bh.rebin(2)] # Modifications can combine with slices
h2 = h[a:b, ...]        # Ellipsis work just like normal numpy
```

\vspace{.5cm}

* [Docs are here](https://boost-histogram.readthedocs.io/en/latest/usage/indexing.html)
* Description may move to a new repository

## Flexibility: Axis types

::: columns
::: {.column width=30%}

* `bh.axis.Regular`
    * `under/overflow`
    * `growth=True`
    * `circular=True`
    * `transform=Log()`
    * `transform=Sqrt()`
    * `transform=Pow(v)`
* `bh.axis.Integer`
    * `under/overflow`
    * `growth=True`
* `bh.axis.Variable`
    * `under/overflow`
    * `growth=True`
* `bh.axis.Category`
   * `int` or `str`
   * `growth=True`

:::
::: {.column width=65%}

\vspace{-.6cm}
\begin{tikzpicture}[every path/.style={thick}, scale=.9]
\begin{scope}[xscale=4, yshift=-.1cm, presDark]
\draw (0,0) -- (1,0);
\foreach \i in {0, .1, ..., 1.1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (0,.2) [above] {0}; 
\node at (.5,.2) [above] {0.5}; 
\node at (1,.2) [above] {1};
\node at (.5,0) [below] {\verb|bh.axis.Regular(10, 0, 1)|};
\end{scope}

\begin{scope}[yshift=-2.3cm, xshift=2cm, presLight2]
\draw (0,0) circle (.75);
\foreach \i in {0, 45, ..., 360} {
    \draw (\i:.75) -- (\i:.95);
}
\node at (0:.95) [right]  {$\pi/2$}; 
\node at (90:.95) [above] {0, $2\pi$};
\node at (180:.95) [left] {$\pi$}; 
\node at (270:.95) [below] {$3\pi/3$}; 
\node at (0,-1.3) [below] {\verb|bh.axis.Regular(8, 0, 2*np.pi, circular=True)|};
\end{scope}

\begin{scope}[xscale=4, yshift=-5.1cm, presDark]
\draw (0,0) -- (1,0);
\foreach \i in {0, .3, .5, 1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (0,.2) [above] {0}; 
\node at (.3,.2) [above] {0.3}; 
\node at (.5,.2) [above] {0.5}; 
\node at (1,.2) [above] {1};
\node at (.5,0) [below] {\verb|bh.axis.Variable([0, .3, .5, 1])|};
\end{scope}

\begin{scope}[xscale=4, yshift=-6.3cm, presLight2]
\draw (0,0) -- (1,0);
\foreach \i in {0, .2, ..., 1} {
    \draw (\i,0) -- (\i,.2);
}
\foreach \i in {0,...,4} {
    \node at (\i/5 + .1, 0) [above] {\i};
}
\node at (.5,0) [below] {\verb|bh.axis.Integer(0, 5)|};
\end{scope}

\begin{scope}[xscale=4, yshift=-7.5cm, presDark]
\draw (0,0) -- (1,0);
\foreach \i in {0, .2, ..., 1} {
    \draw (\i,0) -- (\i,.2);
}
\node at (.1, 0) [above] {2};
\node at (.3, 0) [above] {5};
\node at (.5, 0) [above] {8};
\node at (.7, 0) [above] {3};
\node at (.9, 0) [above] {7};
\node at (.5,0) [below] {\verb|bh.axis.Category([2, 5, 8, 3, 7])|};
\end{scope}


\end{tikzpicture}

:::
:::


## Performance

* Factor of 2 faster than 1D regular binning in Numpy 1.17
    * Currently no specialization, just a 1D regular fill
    * Could be optimized further for common fills
* Factor of 6-10 faster than 2D regular binning Numpy


## Distribution


If `pip install boost-histogram` fails, we have failed.

* Docker ManyLinux1 GCC 9.2: \github{scikit-hep/manylinuxgcc}
* Used in \github{scikit-hep/iMinuit}, \github{scikit-hep/awkward1}
* Described on [`iscinumpy.gitlab.io`](https://iscinumpy.gitlab.io/), also see \github{scikit-hep/azure-wheel-helpers}

::: columns
::: {.column width=60%}

#### Wheels
* manylinux1 32/64 bit, Py 2.7, 3.5, 3.6, 3.7
* manylinux2010 64 bit, Py 2.7, 3.5, 3.6, 3.7, 3.8
* macOS 10.9+ 64 bit, Py 2.7, 3.6, 3.7, 3.8
* Windows 32/64 bit, Py 2.7, 3.6, 3.7

:::
::: {.column width=40%}

#### Source
* SDist on PyPI
* Build directly from GitHub

#### Conda-Forge

* All (including 3.8) except 2.7 on Windows

:::
:::

\vspace{-.4cm}

```bash
python3 -m pip install boost-histogram
conda install -c conda-forge boost-histogram
```

\vspace{.3cm}



## Hist

`hist` is the 'wrapper' piece that does plotting and interacts with the rest of the ecosystem.

::: columns
::: {.column width=60%}

#### Plans

* Easy plotting adaptors (mpl-hep)
* Serialization formats via aghast (ROOT, HDF5)
* Implicit multithreading
* Statistical functions (Like TEfficiency)
* Multihistogram plotting (HistBook)
* Interaction with fitters (ZFit, GooFit, etc)
* Bayesian Blocks algorithm from Scikit-HEP
* Command line histograms for stream of numbers

:::
::: {.column width=35%}

#### Call for contributions

* What do you need?
* Looking for a student interested in development

####
Join in the development! This should combine the best features of other packages.

:::
:::

## aghast

::: columns
::: {.column width=65%}

\begin{center}
\includegraphics[width=.3\textwidth]{images/AghastLogo.png}
\end{center}


\vspace{.5cm}

aghast is a histogramming library that does not fill histograms and does not plot them.

\vspace{.5cm}

\github{scikit-hep/aghast}

\vspace{.5cm}

* A memory format for histograms, like Apache Arrow
* Converts to and from other libraries
* Uses flatbuffers to hold histograms
* Indexing ideas inspired the UHI


:::
::: {.column width=35%}

### Available Binnings


* `IntegerBinning`
* `RegularBinning`
* `HexagonalBinning`
* `EdgesBinning`
* `IrregularBinning`
* `CategoryBinning`
* `SparseRegularBinning`
* `FractionBinning`
* `PredicateBinning`
* `VariationBinning`

:::
:::

# IRIS-HEP AS: Other projects

## IRIS-HEP AS: Particle and DecayLanguage

::: columns
::: column

\begin{center}
\includegraphics[width=.5\textwidth]{images/ParticleLogo300.png}
\end{center}

* Python library for PDG particle information
* Intuitive interface, LaTeX/HTML names, and much more
* New: ID conversion tools, GeantID, more

:::
::: column

\begin{center}
\includegraphics[width=.5\textwidth]{images/DecayLanguage200.png}
\end{center}

* Python library for decay descriptions
* Reads DEC files, also AmpGen utilities

:::
:::

## IRIS-HEP AS: ROOT on Conda-Forge

The fastest, most universal way to get ROOT on macOS or Linux, for Python and more!

::: columns
::: {.column width=30%}

### Packages

* ROOT 6.18/04
* Pythia8
* Uproot
* HEPMC2
* HEPMC3
* xrootd

:::
::: {.column width=70%}

\vspace{.75cm}

* Full support for environments
    * no `thisroot.sh`
* Easy way to have Python 2 & 3
    * Only known place to get 3.8 ATM
* Supports compiling, C++17, JupyROOT, and more
* Fully reproducible from `environment.yml`
* Runs on mybinder, docker, and more

:::
:::

And many more HEP tools!

## IRIS-HEP AS: Awkward family

::: columns
::: {.column width=40%}

* Proposed new package `vector`
* Built on HEPVector, Scikit-HEP Vector, and uproot-methods
* Fits together with Awkward

:::
::: {.column width=60%}

\includegraphics[width=\textwidth]{plans/ScikitHEPFamilyTree.pdf}

:::
:::

## IRIS-HEP AS: Awkward family

\begin{center}
\includegraphics[width=.95\textwidth]{plans/ScikitHEPTimeline.pdf}
\end{center}


# IRIS-HEP IA

## IRIS-HEP IA: PV-Finder


\begin{center}
\includegraphics[width=8cm]{images/pvfinderlogo.png}
\end{center}

::: columns
::: column
* Custom 3D hits to 1D kernel
* Based on toy LHCb detector
:::
::: column

* Developing CNN network to find PVs
* Achieving 93%+ accuracy

:::
:::

## IRIS-HEP IA: PV-Finder


\colorlet{light}{presLight!35!white}
\colorlet{dark}{presDark2}
\colorlet{hl}{presLight2}
\colorlet{hd}{presDark}
\begin{tikzpicture}[
    scale=.95,
    belowbox/.style={fill=light},
    topbox/.style={fill=dark},
    texttop/.style={below, white},
    track/.style={thick, dark},
    minibox/.style={draw, thick, minimum width=.1cm, minimum height=.1cm, dark},
    miniarr/.style={->, thick, dark},
    conn/.style={-latex, ultra thick, dark}
    ]

    	\begin{scope}
    	    \path [belowbox] (-1.5,-1) rectangle (1.5,.45);
    		\draw [track] (0,-.275) -- (-1.5,-.9);
    		\draw [track] (0,-.275) -- (-1.5,-.6);
    		\draw [track] (0,-.275) -- (-1.5,.2);
    		\draw [track] (0,-.275) -- (1.5,-.3);
    		\draw [track] (0,-.275) -- (1.5,-.6);
    		\draw [track] (0,-.275) -- (1.5,.85);
    		\draw [track] (0,-.275) -- (-.5,-1);
    		\draw [track] (.4, -.6) -- (1.3,-1);
    		\draw [track] (.4, -.6) -- (.7,-1);
    		\path [topbox]  (-1.5,.45) rectangle (1.5,1);
    	    \node at (0,1) [texttop] {Tracking};
            \path [fill=hl] (0, -.275) circle (.05);
            \path [fill=hd] (.4, -.6) circle (.05);
    	\end{scope}

    	\begin{scope}[xshift=4cm]
    	    \path [belowbox] (-1.5,-1) rectangle (1.5,.45);
    		\path [fill=dark] plot [smooth] coordinates {
    			(-1.5,-1)
    			(-1.1, -.98)
    			(-.9, -.9)
    			(-.5, -.6)
    			(0, .2)
    			(.2, -.2)
    			(.4, 0)
    			(.8, -.7)
    			(1.1, -.9)
    			(1.3, -.98)
    			(1.5,-1)
    		};
    		\path [topbox]  (-1.5,.45) rectangle (1.5,1);
    	    \node at (0,1) [texttop] {Kernel generation};
    	\end{scope}

    	\begin{scope}[xshift=8cm]
    	    \path [belowbox] (-1.5,-1) rectangle (1.5,.45);

            \begin{scope}[yshift=3mm]
        		\node [minibox] (mA) at (-1,-.3) {};
        		\node [minibox] (mB) at (-.5,-.3) {};
        		\node [minibox] (mC) at (0,-.3) {};
        		\node [minibox] (mD) at (.5,-.3) {};
        		\node [minibox] (mE) at (1,-.3) {};
        		\draw [miniarr] (mA) -- (mB);
        		\draw [miniarr] (mB) -- (mC);
        		\draw [miniarr] (mC) -- (mD);
        		\draw [miniarr] (mD) -- (mE);
            \end{scope}

    		\path [topbox]  (-1.5,.45) rectangle (1.5,1);
    	    \node at (0,1) [texttop] {Make predictions};
            \node at (0,-.5) [dark] {CNNs};
    	\end{scope}

    	\begin{scope}[xshift=12cm]
    	    \path [belowbox] (-1.5,-1) rectangle (1.5,.45);
    		\path [fill=dark] plot [smooth] coordinates {
    		(-.15,-1)
    		(0, 0)
    		(.15,-1)
    		};
    		\path [fill=dark] plot [smooth] coordinates {
    		(.25,-1)
    		(.4, 0)
    		(.55,-1)
    		};
    		\path [topbox]  (-1.5,.45) rectangle (1.5,1);
    	    \node at (0,1) [texttop] {Interpret results};
    	\end{scope}

    	\begin{scope}[xshift=0cm, yshift=-2.5cm]
    	    \path [belowbox] (-1.5,-1) rectangle (1.5,.45);
    		\path [fill=hl] plot [smooth] coordinates {
    		(-.15,-1)
    		(0, 0)
    		(.15,-1)
    		};
    		\path [fill=hd] plot [smooth] coordinates {
    		(.25,-1)
    		(.4, 0)
    		(.55,-1)
    		};
    		\path [topbox]  (-1.5,.45) rectangle (1.5,1);
    	    \node at (0,1) [texttop] {Truth};
    	\end{scope}

    	\draw [conn] (1.5,0) -- (2.5,0);
    	\draw [conn] (5.5,0) -- (6.5,0);
    	\draw [conn] (9.5,0) -- (10.5,0);
    	\draw [conn] (1.5, -2.5) -- (7, -1.1) node [above=-2.5, midway, rotate=13] {Training};
    	\draw [conn] (1.5, -3) -- (11, -1.1) node [above=-1, midway, rotate=11] {Validation};

\end{tikzpicture}



# IRIS-HEP Other

## IRIS-HEP SSC: USATLAS Bootcamp

Very successful Bootcamp at LBNL!

Each day started with a general lesson, followed by a matching ATLAS lesson.

| Topic | Name | Source |
|---|---|---|
| Version Control | Intro to Git | Software Carpentries |
| Build Systems | Modern CMake | IRIS-HEP |
| Continuous Integration & Testing | CI/CD and Python Testing | Software Carpentries |
| Containers | Intro to Docker | IRIS-HEP |

Much of this material is reusable!

## IRIS-HEP SSC: CMake workshop


::: columns
::: {.column width=30%}

\vspace{.5cm}

* Based on my book, "Modern CMake"
* Targeting eventual inclusion in Software Carpentries for HEP

::: 
::: {.column width=70%}

\includegraphics[width=\textwidth, trim=50 50 50 50]{screenshots/CMakeExample.png}

:::
:::

## IRIS-HEP Website

::: columns
::: {.column width=30%}

\vspace{.5cm}

* Work on IRIS-HEP website and Ruby plugins
    - Indico and InspireHEP lookup plugins
    - Validation system in Travis
    - Dark Mode
    - Front page layout, news story layout
    - Sorting for presentations, publications

::: 
::: {.column width=70%}

\only<1>{\includegraphics[width=\textwidth, trim=50 50 50 50]{screenshots/IRISLight.png}}
\only<2>{\includegraphics[width=\textwidth, trim=50 50 50 50]{screenshots/IRISDark.png}}

:::
:::


# Questions?

