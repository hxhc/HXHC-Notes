---
{"permalink":"/1-projects/algo/chemometrics/savitkzy-golay-filter/"}
---
```python
import numpy as np
from math import factorial
from scipy.linalg import lstsq


def sg_coef(window_length=15, polyorder=2, deriv=1):
    # window_length must be odd
    # polyorder must be greater than deriv

    # window_length = 15
    # polyorder = 2
    # deriv = 1
	
    m = (window_length - 1) / 2    # 7 by default

    x = np.arange(-m, window_length - m, dtype=float)
    # [-7, -6, ...,0..., 1, 2, .., 7]

    order = np.arange(polyorder + 1).reshape(-1, 1)    # [0, 1, 2]
    A = x ** order    # create a design matrix, size=(polyorder+1, window_length)
    """
    A =
        [[1, 1, 1, 1, 1, ..., 1, 1, 1, 1, 1]
         [-7, -6, -5, ...,0, 1, 2,..., 6, 7]
         [49, 36, 25, ...,0, 1, 4,  ..., 49]]
    """
    y = np.zeros(polyorder + 1)    # y= [0.0, 0.0, 0.0]
    y[deriv] = float(factorial(deriv))    # y=[0.0, 1.0, 0.0]

    coef, _, _, _ = lstsq(A, y)  # 最小二乘拟合

    # A.dot(coef) = y

    return coef