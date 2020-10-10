# PySR.jl

[![DOI](https://zenodo.org/badge/295391759.svg)](https://zenodo.org/badge/latestdoi/295391759)
[![PyPI version](https://badge.fury.io/py/pysr.svg)](https://badge.fury.io/py/pysr)
[![Build Status](https://travis-ci.com/MilesCranmer/PySR.svg?branch=master)](https://travis-ci.com/MilesCranmer/PySR)

**Symbolic regression built on Julia, and interfaced by Python.
Uses regularized evolution, simulated annealing, and gradient-free optimization.**

Symbolic regression is a very interpretable machine learning algorithm
for low-dimensional problems: these tools search equation space
to find algebraic relations that approximate a dataset.

One can also
extend these approaches to higher-dimensional
spaces by using a neural network as proxy, as explained in 
https://arxiv.org/abs/2006.11287, where we apply
it to N-body problems. Here, one essentially uses
symbolic regression to convert a neural net
to an analytic equation. Thus, these tools simultaneously present
an explicit and powerful way to interpret deep models.


*Backstory:*

Previously, we have used
[eureqa](https://www.creativemachineslab.com/eureqa.html),
which is a very efficient and user-friendly tool. However,
eureqa is GUI-only, doesn't allow for user-defined
operators, has no distributed capabilities,
and has become proprietary (and recently been merged into an online
service). Thus, the goal
of this package is to have an open-source symbolic regression tool
as efficient as eureqa, while also exposing a configurable
python interface.


# Installation
PySR uses both Julia and Python, so you need to have both installed.

Install Julia - see [downloads](https://julialang.org/downloads/), and
then instructions for [mac](https://julialang.org/downloads/platform/#macos)
and [linux](https://julialang.org/downloads/platform/#linux_and_freebsd).
(Don't use the `conda-forge` version; it doesn't seem to work properly.)
Then, at the command line,
install the `Optim` and `SpecialFunctions` packages via:

```bash
julia -e 'import Pkg; Pkg.add("Optim"); Pkg.add("SpecialFunctions")'
```

For python, you need to have Python 3, numpy, sympy, and pandas installed.

You can install this package from PyPI with:

```bash
pip install pysr
```

# Quickstart

```python
import numpy as np
from pysr import pysr

# Dataset
X = 2*np.random.randn(100, 5)
y = 2*np.cos(X[:, 3]) + X[:, 0]**2 - 2

# Learn equations
equations = pysr(X, y, niterations=5,
            binary_operators=["plus", "mult"],
            unary_operators=["cos", "exp", "sin"])

...

print(equations)
```

which gives:

```
   Complexity       MSE                                                Equation
0           5  1.947431                          plus(-1.7420927, mult(x0, x0))
1           8  0.486858           plus(-1.8710494, plus(cos(x3), mult(x0, x0)))
2          11  0.000000  plus(plus(mult(x0, x0), cos(x3)), plus(-2.0, cos(x3)))
```

The newest version of PySR also returns three additional columns:

- `score` - a metric akin to Occam's razor; you should use this to help select the "true" equation.
- `sympy_format` - sympy equation.
- `lambda_format` - a lambda function for that equation, that you can pass values through.

