# Lights Out Puzzle: Mathematical Modeling and Optimization

A mathematical programming formulation of the Lights Out puzzle, implemented in Python and solved as an Operations Research optimization problem.

---

# 1. Problem Description

In this project, a problem known as the **Lights Out Puzzle** is studied.

The input is an $n \times m$ binary matrix whose entries can only take values 0 or 1. A value of 1 indicates that the corresponding cell is lit, while a value of 0 indicates that the cell is unlit.

At each step, a cell can be selected (clicked). Clicking a cell toggles not only the state of the selected cell itself, but also the states of its four orthogonal neighbors (up, down, left, and right). Toggling means that a value of 1 becomes 0 and a value of 0 becomes 1.

The objective of the problem is to determine a set of clicks such that, after applying all clicks, every cell in the matrix becomes unlit. Furthermore, the total number of clicks must be minimized. Therefore, the problem can be formulated as a **Binary Integer Programming** model.

---

# 2. Mathematical Model

In this section, the sets, indices, parameters, and decision variables are introduced, followed by the mathematical formulation of the problem.

## Table 1. Sets

| Symbol | Description |
|----------|----------|
| $I$ | Set of row indices, $I=\{1,\dots,n\}$ |
| $J$ | Set of column indices, $J=\{1,\dots,m\}$ |
| $N(i,j)$ | Set of cells whose clicks affect cell $(i,j)$ |

---

## Table 2. Indices

| Index | Description |
|----------|----------|
| $i$ | Row index |
| $j$ | Column index |
| $a$ | Row index of a neighboring cell |
| $b$ | Column index of a neighboring cell |

---

## Table 3. Parameters

| Parameter | Description |
|----------|----------|
| $n$ | Number of matrix rows |
| $m$ | Number of matrix columns |
| $c_{ij}$ | Initial state of cell $(i,j)$ |

---

## Table 4. Decision Variables

| Variable | Description |
|----------|----------|
| $x_{ij}$ | Binary variable; equals 1 if cell $(i,j)$ is clicked and 0 otherwise |
| $y_{ij}$ | Auxiliary nonnegative integer variable used to model the parity condition |

---

The mathematical model is formulated as follows.

### (1)

$$
N(i,j)=\lbrace (i,j) \rbrace \cup \lbrace (i-1,j):i \gt 1 \rbrace \cup \lbrace (i+1,j):i \lt n \rbrace \cup \lbrace (i,j-1):j \gt 1 \rbrace \cup \lbrace (i,j+1):j \lt m \rbrace
$$

where $N(i,j)$ contains the cell itself and all valid orthogonal neighbors.

### (2)

$$
\min Z=\sum_{i\in I}\sum_{j\in J}x_{ij}
$$

subject to


### (3)

$$
c_{ij} + \sum_{(a,b)\in N(i,j)} x_{ab} = 2y_{ij}, \qquad \forall i \in I, \, j \in J
$$
### (4)

$$
x_{ij}\in\{0,1\},
\qquad
\forall i\in I,\;j\in J
$$

### (5)

$$
0\le y_{ij}\le 5,
\qquad
y_{ij}\in\mathbb Z,
\qquad
\forall i\in I,\;j\in J
$$

---

# 3. Model Explanation

Equation (1) defines the neighborhood of each cell. This neighborhood consists of the cell itself and its four orthogonal neighbors. For boundary and corner cells, some of these neighbors may not exist.

Equation (2) represents the objective function. The goal is to minimize the total number of clicks performed on the board. Since each variable $x_{ij}$ indicates whether a click is performed on cell $(i,j)$, the sum of all $x_{ij}$ variables corresponds to the total number of clicks.

Equation (3) is the most important constraint in the model. Each click toggles the selected cell and its neighboring cells. Therefore, the number of times cell $(i,j)$ is affected is equal to the sum of the decision variables associated with all cells belonging to the set $N(i,j)$. The indices $a$ and $b$ are used to enumerate the elements of this set.

For a cell to be turned off in the final configuration, the sum of its initial state and the number of times it is toggled must be an even number. The auxiliary variable $y_{ij}$ is used to model this parity condition. Consequently, Equation (3) guarantees that the final state of every cell is equal to zero.

Equation (4) specifies that the decision variables $x_{ij}$ are binary. Thus, each cell is either selected or not selected.

Equation (5) defines the domain of the auxiliary variables $y_{ij}$. Since at most five cells can affect a given cell, an upper bound of 5 is sufficient.

---

# 4. Computational Results

The proposed model was implemented in **Python/Pyomo** and solved using the **GLPK** solver.

The input matrix used for evaluation is shown below.

### (6)

$$
A=
\begin{bmatrix}
1&0&1&0&1\\
0&0&0&0&0\\
1&0&0&1&0\\
1&1&0&0&1\\
1&1&1&1&1
\end{bmatrix}
$$

After solving the model, the program returned the optimal decision matrix $X^*$. In this matrix, a value of 1 indicates that the corresponding cell is clicked, while a value of 0 indicates that no click is performed.

### (7)

$$
X^*=
\begin{bmatrix}
1&1&0&0&0\\
1&0&0&0&1\\
0&0&0&1&1\\
0&0&1&1&1\\
1&0&0&0&0
\end{bmatrix}
$$

According to Equation (8), the optimal objective value is equal to 10.

### (8)

$$
Z^*=10
$$

This value represents the minimum number of clicks required to turn off all cells in the matrix. Therefore, no feasible solution exists with fewer than 10 clicks.

To validate the obtained solution, the clicks represented by $X^*$ were applied to the initial matrix. The resulting final matrix is shown below.

### (9)

$$
A'=
\begin{bmatrix}
0&0&0&0&0\\
0&0&0&0&0\\
0&0&0&0&0\\
0&0&0&0&0\\
0&0&0&0&0
\end{bmatrix}
$$

According to Equation (9), after applying the optimal clicks, all entries of the final matrix become zero. Hence, the requirement of completely turning off the board is satisfied.

---

# Academic Context

This repository contains the implementation and mathematical formulation of a Lights Out optimization problem developed for an Operations Research assignment at the University of Tehran.

**Assignment webpage:**  
https://malekiaan.ir/or2/ex1/

**Instructor:** Dr. Yaser Malekian

**Institution:** University of Tehran
