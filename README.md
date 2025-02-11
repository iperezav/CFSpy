<div align="center">
<img src="https://raw.githubusercontent.com/iperezav/CFSpy/main/logo/CFSpy_logo.png" alt="CFSpy" / >

---

[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/cfspy)][py-versions]
[![PyPI - Version](https://img.shields.io/pypi/v/cfspy)][pypi-latest-version]
![PyPI - Status](https://img.shields.io/pypi/status/cfspy)
[![PyPI - Downloads](https://img.shields.io/pypi/dd/cfspy)][downloads]
[![PyPI - License](https://img.shields.io/pypi/l/cfspy)][license]

</div>

# CFSpy

CFSpy is a package to simulate the output of a control system by means of the Chen-Fliess series.

It provides:

- The list of iterated integrals indexed by words of a certain length or less. 
- The list of Lie derivatives indexed by words of a certain length or less.
- A single iterated integral indexed by a given word.
- A single Lie derivative indexed by a given word.


## Overview

CFSpy is a Python library that contains the following functions:

| Function | Description |
| ---- | --- |
| [**iter_int**](https://github.com/iperezav/CFSpy/blob/main/build/lib/CFS/iter_int.py) | A function for the numerical computation of a list of iterated integrals |
| [**iter_lie**](https://github.com/iperezav/CFSpy/blob/main/build/lib/CFS/iter_lie.py) | A function for the analytical computation of a list of Lie derivatives |
| [**single_iter_int**](https://github.com/iperezav/CFSpy/blob/main/build/lib/CFS/single_iter_int.py) | A function for the numerical computation of a single iterated integral |
| [**single_iter_lie**](https://github.com/iperezav/CFSpy/blob/main/build/lib/CFS/single_iter_lie.py) | A function for the analytical computation of a single Lie derivative |

CFSpy is used for:

- Simulation of the output of a control systems.
- Reachability analysis of a control system.


# Installation 
Currently, `CFSpy` supports releases of Python 3.12.4 onwards.
To install the current release:

```shell
$ pip install --upgrade CFSpy
```


# Getting Started

## Minimal Example
```python
from CFS import iter_int, iter_lie, single_iter_int, single_iter_lie

import numpy as np
from scipy.integrate import solve_ivp
import matplotlib.pyplot as plt
import sympy as sp

# Define the Lotka-Volterra system
def system(t, x, u1_func, u2_func):
    x1, x2 = x
    u1 = u1_func(t)
    u2 = u2_func(t)
    dx1 = -x1*x2 +  x1 * u1
    dx2 = x1*x2 - x2* u2
    return [dx1, dx2]

# Input 1
def u1_func(t):
    return np.sin(t)

# Input 2
def u2_func(t):
    return np.cos(t)

# Initial condition
x0 = [1/3,2/3]

# Time range
t0 = 0
tf = 3
dt = 0.001
t_span = (t0, tf)

# Simulation of the system
solution = solve_ivp(system, t_span, x0, args=(u1_func, u2_func), dense_output=True)

# Partition of the time interval
t = np.linspace(t_span[0], t_span[1], int((tf-t0)//dt+1))
y = solution.sol(t)

# Define the symbolic variables
x1, x2 = sp.symbols('x1 x2')
x = sp.Matrix([x1, x2])


# Define the system symbolically
g = sp.transpose(sp.Matrix([[-x1*x2, x1*x2], [x1, 0], [0, - x2]]))

# Define the output symbolically
h = x1

# The truncation of the length of the words that index the Chen-Fliess series
Ntrunc = 4

# Coefficients of the Chen-Fliess series evaluated at the initial state
Ceta = np.array(iter_lie(h,g,x,Ntrunc).subs([(x[0], 1/3),(x[1], 2/3)]))

# inputs as arrays
u1 = np.sin(t)
u2 = np.cos(t)

# input array
u = np.vstack([u1, u2])

# List of iterated integral
Eu = iter_int(u,t0, tf, dt, Ntrunc)

# Chen-Fliess series
F_cu = x0[0]+np.sum(Ceta*Eu, axis = 0)

# Graph of the output and the Chen-Fliess series
plt.figure(figsize = (12,5))
plt.plot(t, y[0].T)
plt.plot(t, F_cu, color='red', linewidth=5, linestyle = '--', alpha = 0.5)
plt.xlabel('$t$')
plt.ylabel('$x_1$')
plt.legend(['Output of the system','Chen-Fliess series'])
plt.grid()
plt.show()
```
<img src="https://raw.githubusercontent.com/iperezav/CFSpy/main/examples/output_chenfliess.png" alt="iter_int(), iter_lie()" />

For more examples, see the [CFSpy demos](https://github.com/iperezav/CFSpy/blob/main/examples/)


# Resources

- [**PyPi**](https://pypi.org/project/CFSpy/)
- [**Documentation**](https://github.com/iperezav/CFSpy/blob/main/README.md)
- [**Issue tracking**](https://github.com/iperezav/CFSpy/issues)


# Contributing

All feedback is welcome. 


# Asking for help
Please reach out if you have any questions:
1. [Github CFSpy discussions](https://github.com/iperezav/CFSpy/discussions/).
2. [Github CFSpy issues](https://github.com/iperezav/CFSpy/issues).


# License

CFSpy is open-source and released under the [MIT License](LICENSE).


# BibTeX
Feel free to cite my work:

```bibtex
@article{iperezave,
  title={CFSpy},
  author={Perez Avellaneda, Ivan},
  journal={GitHub. Note: https://github.com/iperezav/CFSpy},
  volume={1},
  year={2024}
}
```

[issues]: https://github.com/iperezav/CFSpy/issues
[demos]: https://github.com/iperezav/CFSpy/blob/main/examples/

[downloads]: https://pepy.tech/projects/cfspy
[py-versions]: https://pypi.org/project/cfspy/
[pypi-latest-version]: https://pypi.org/project/cfspy/
[license]: https://github.com/iperezav/CFSpy/blob/main/LICENSE