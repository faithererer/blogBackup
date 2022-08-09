---
title: 数模の路---线性规划
tags: 
mathjax: true
cover: 
categories: "数学建模"
---





# 线性规划

我想计算一个线性方程组方程组的解
$$
\begin{equation}
% \begin{equation*} 加'*'去掉公式编号
\left\{
\begin{aligned}     %请使用'aligned'或'align*'
10x-2y-2z &= 72 \\     %加'&'指定对齐位置
-x+10y-2z &= 83 \\
-x-y+5z &= 42 \\
\end{aligned}
\right.
\end{equation}
% \end{equation*}   加'*'去掉公式编号

% 注意：在 markdown 环境下，某些特殊字符，如'\', '*'等，会首先被 markdown 语法转义，然后再被 Latex 转义。
% 因此有时候 '\{'需要写作'\\{'，'*'需要写作'\*'，'\\'需要写作'\\\\'等，视不同的解释环境而定
$$
用python算应该怎么办呢？

发现有一个叫`numpy`的库

它有啥我们先不管,我们先关注于问题本身

它是一个$AX=b$形式的向量,我可以把它们写成向量的形式啊

长这样：
$$
\left[
\begin{matrix}
10 & -2 & -2 \\
-1 & 10 & -2 \\
-1 & -1 & 5 \\
\end{matrix}
\right]

 \left[
\begin{matrix}
x\\ y\\ z
\end{matrix}
\right]
=
\left[
\begin{matrix}
72\\
83\\
42\\
\end{matrix}
\right]
$$

我需要矩阵乘法和矩阵求逆的方法
```python
import numpy as np

A = np.array([[10, -2, -2],[-1, 10, -2],[1, -1, 5]])
b = np.array([72, 83, 42])
X = np.linalg.inv(A).dot(b)

print(X)

```
我还发现可以用一个方法来解方程`$AX=b$`

```python
X = np.linalg.solve(A,b)
```
它是求解满秩的方程组的函数
第一个是系数矩阵，第二个是常数矩阵


我又了解到一个叫`sympy`的库,它的计算精度似乎更高
```python
from sympy import symbols, Eq, solve

x,y,z = symbols("x y z")  # 符号化变量
equ =[Eq(10*x-2*y-2*z, 72),  # 等式函数
    Eq(-x+10*y-2*z, 83),
    Eq(-x-y+5*z, 72)]

print(solve(equ, [x,y,z]))  #
第一个参数为要解的方程，要求右端等于0，第二个参数为要解的未知数
```
可是遇到很多不同类型约束条件应该怎么办呢
## 线性规划的标准形式
$min \quad c^Tx$

$$
\begin{equation}
\left\{
    \begin{aligned} 
    Ax &\leq b \\
    Aeq·x&=Beq \\
    Ib \leq &x \leq ub
    \end{aligned} 
\right.
\end{equation}
$$

比如下面这个 

$max\quad z=2x_1+3x_2-5x_3$


$$
\begin{equation}
\left\{
    \begin{aligned} 
    x_1+x_2+x_3&=7 \\
    2x_1-5x_2+x_3&\geq10 \\
    x_1+3x_2+x_3&\leq12 \\
    x_1,x_2,x_3&\geq 3
    \end{aligned} 
\right.
\end{equation}
$$

**求一个变量的最大值我们只要求出这个变量的相反数的最小值就好了**

根据规范式转换为

$min\quad -z=-2x_1-3x_2+5x_3$

$$
\begin{equation}
\left\{
    \begin{aligned} 
    x_1+x_2+x_3&=7 \\
    -2x_1+5x_2-x_3&\leq-10 \\
    x_1+3x_2+x_3&\leq12 \\
    -x_1,-x_2,-x_3&\leq-3
    \end{aligned} 
\right.
\end{equation}
$$
```python
from pickletools import optimize
import numpy as np
from scipy.optimize import linprog

c = np.array([-2,-3,5])
Aeq = np.array([[1,1,1]])
beq = np.array([7])
A = np.array([[-2,5,-1],[1,3,1]])
b = np.array([-10,12])
x1,x2,x3 = (0,None),(0,None),(0,None)

res = linprog(c,A,b,Aeq,beq,bounds=[x1,x2,x3])
print(res)
```

关于`scipy.optimize.linprog`
[详细信息](https://blog.csdn.net/weixin_45288557/article/details/109139303)
