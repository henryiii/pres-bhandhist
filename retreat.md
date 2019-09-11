---
title: |
  Histogramming & more
author: Henry Schreiner
date: September 12, 2019
fontsize: 10pt
aspectratio: 169
header-includes: |
    \usetikzlibrary{decorations.pathreplacing}
---


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
\node (accum) at (1.2,1.5) [circle, minimum width=.7cm, draw, presLight2] {};
\draw [presLight2, latex-] (accum) -- (2.25, .5) node [right, align=left] {Accumulator\\int, double,\\unlimited, ...};

\end{tikzpicture}
\end{center}
\vspace{-.1cm}


:::
:::



# AS: boost-histogram (Python)


\github{scikit-hep/boost-histogram}

* 0-dependency build (C++14 only)
* State-of-the-art PyBind11
* 280+ unit tests run on Azure on Linux, macOS, and Windows
* Binary wheels on Azure for all major platforms
    - Machinery already in use for iMinuit, helping other Scikit-HEP packages
- [Read the docs (in progress)](https://boost-histogram.readthedocs.io/en/latest/)

\vspace{.5cm}

\begin{center}
\begin{tikzpicture}[scale=1.25]
\node [rectangle, rounded corners, white, fill=presDark, minimum width=2.2cm, minimum height=.75cm] at (0,0) {Design};
\node [rectangle, rounded corners, white, fill=presLight2, minimum width=2.2cm, minimum height=.75cm] at (2.5,0) {\vphantom{g}Flexibility};
\node [rectangle, rounded corners, white, fill=presDark2, minimum width=2.2cm, minimum height=.75cm] at (5,0) {Speed};
\node [rectangle, rounded corners, white, fill=presLight, minimum width=2.2cm, minimum height=.75cm] at (7.5,0) {\vphantom{g}Distribution};
\end{tikzpicture}
\end{center}


# Design demo

\textcolor{red}{Note: Some details may change}

::: columns
::: column

```python
from pyforest import *
import boost.histogram as bh

# New histogram composition
hist = bh.histogram(
    bh.axis.regular(20, -1, 1),
    bh.axis.variable([-1,-.3,0,.2,.8,1])
)

# Fill with arrays
hist.fill(
    np.random.normal(size=10_000),
    np.random.normal(size=10_000)
)
```
:::
::: column

```python
# Convert bins to numpy array
# Can keep or remove flow bins
nparr = hist.view(flow=True)

# UHI: Unified Histogram Indexing
pr = hist[::bh.rebin(2), ...]

# Scale
hist2 = hist * 2

# Sum
hist2.sum()
```

:::
:::



# 22 axis types

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
\vspace{1em}

:::
:::




# AS: Hist
`hist` is the 'wrapper' piece that does plotting and interacts with the rest of the ecosystem.

## Plans

* Easy plotting adaptors (mpl-hep)
* Serialization formats (ROOT, HDF5)
* Auto-multithreading
* Statistical functions (Like TEfficiency)
* Multihistograms (HistBook)
* Interaction with fitters (ZFit, GooFit, etc)
* Bayesian Blocks algorithm from SciKit-HEP
* Command line histograms for stream of numbers


# Future of histograms in Python

\begin{centering}
\begin{tikzpicture}[scale=1.5, every path/.style={presDark, thick}]
\node at (-2.5,0) [left, presDark2] {\vphantom{yH}Core histogramming libraries};
\node (bh) at (-.5,0)   [presDark2]  {\vphantom{yH}boost-histogram};
\node (root) at (2,0)   [presDark2] {\vphantom{yH}ROOT};

\node at (-2.5,-1) [left, presLight2] {\vphantom{yH}Universal adaptor};
\node (aghast) at (0,-1) [presLight2] {\vphantom{yH}Aghast};

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

# AS: Particle and DecayLanguage

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

# AS: ROOT on Conda-Forge

The fastest, most universal way to get ROOT on macOS or Linux, for Python and more!

::: columns
::: {.column width=30%}

## Packages

* ROOT 6.18/00
* Pythia8
* Uproot

:::
::: {.column width=70%}

* Full support for environments
    * no `thisroot.sh`
* Easy way to have Python 2 & 3
* Supports compiling, C++17, JupyROOT, and more
* Fully reproducible from `environment.yml`
* Runs on mybinder, docker, and more

:::
:::

And many more HEP tools!

# IA: PV-Finder

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
      \node at (10,-3.2) {\includegraphics[width=6cm]{images/pvfinderlogo.png}};

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

# SSC: USATLAS Bootcamp

Very successful Bootcamp at LBNL!

Each day started with a general lesson, followed by a matching ATLAS lesson.

| Topic | Name | Source |
|---|---|---|
| Version Control | Intro to Git | Software Carpentries |
| Build Systems | Modern CMake | IRIS-HEP |
| Continuous Integration & Testing | CI/CD and Python Testing | Software Carpentries |
| Containers | Intro to Docker | IRIS-HEP |

Much of this material is reusable!

