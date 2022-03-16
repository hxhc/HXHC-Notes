---
{"permalink":"/3-web-clip/mathjax-test/"}
---

1. Tag support
```latex
$$a^2+b^2=c^2 \tag {1.1}$$
```


$$a^2+b^2=c^2 \tag {1.1}$$
The tag of the above equation is ${1.1}$

2. Line break support
```latex
$$
\begin{split}
a &= b \\
c &= d \\
e &= f
\end{split}\tag{1.2}
$$
```
$$
\begin{split}
a &= b \\
c &= d \\
e &= f
\end{split}\tag{1.2}
$$
This should render 3 lines, but may only render the 3 equations in just 1 line.
Try the following code:
```latex
$$
\begin{split}
a &= b \newline
c &= d \newline
e &= f
\end{split}\tag{1.3}
$$
```
$$\begin{split}
a &= b \newline
c &= d \newline
e &= f
\end{split}\tag{1.3}$$

3. Equation array support
```latex
$$
\begin{eqnarray*}
x^n+y^n &=& z^n \tag{1.4} \newline
x+y &=& z \tag{1.5}
\end{eqnarray*}
$$
```
$$
\begin{eqnarray*}
x^n+y^n &=& z^n \tag{1.4} \newline
x+y &=& z \tag{1.5}
\end{eqnarray*}
$$