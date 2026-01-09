# Theory: Quantum Singular Value Transformation (QSVT)

This document provides the theoretical background underlying the notebooks in this repository. It is intentionally concise and focused on the concepts that are *directly exercised* in the examples, rather than a full survey of the QSVT literature.

The emphasis is on:
- how **polynomials act on singular values** via quantum circuits,
- why **boundedness and parity constraints** appear,
- how **QSP emerges as a special case**, and
- how **linear-system–style transformations** arise from polynomial approximations to $1/x$.

---

## 1. Block encodings

QSVT operates on matrices that are provided via a **block encoding**.

### Definition (block encoding)

A unitary $U$ acting on an enlarged Hilbert space is said to be a block encoding of a matrix $A$ if

$$U =
\begin{pmatrix}
A / \alpha & * \\
* & *
\end{pmatrix},
$$

for some normalization constant $\alpha \ge 1$, where the top-left block acts on a designated “logical” subspace.

Operationally, this means that when the ancilla qubits are prepared in $|0\rangle$, the action of $U$ on the logical register reproduces the action of $A$ (up to normalization).

### In this repository

- PennyLane’s `block_encoding="embedding"` is used.
- This automatically constructs a valid block encoding for small, explicitly specified matrices.
- The notebooks frequently **extract the top-left block** of the resulting unitary to verify that it matches the expected transformed operator.

This is a simulator-friendly convenience; the theoretical statements of QSVT apply to *any* valid block encoding.

---

## 2. Singular values, eigenvalues, and normalization

QSVT acts on **singular values** $\sigma_i \in [0,1]$. For Hermitian matrices, singular values coincide with absolute eigenvalues.

To apply QSVT:
- The spectrum must be rescaled so that $\sigma_i \in [-1,1]$ (or $[0,1]$ depending on parity).
- All examples in this repository are chosen so that eigenvalues already lie in $[-1,1]$, avoiding additional normalization steps.

---

## 3. Quantum Singular Value Transformation (QSVT)

### Informal statement

Given:
- a block encoding of a matrix $A$,
- and a real polynomial $f$ satisfying certain constraints,

QSVT constructs a quantum circuit whose effective action applies

$$
\sigma_i \;\longmapsto\; f(\sigma_i)
$$

to each singular value $\sigma_i$ of $A$.

In other words, QSVT implements **matrix functions via polynomial transformations**.

### Admissibility constraints

The polynomial $f(x)$ must satisfy:

1. **Boundedness**
   $$
   |f(x)| \le 1 \quad \text{for all } x \in [-1,1].
   $$

2. **Parity structure**
   - Even polynomials act on singular values.
   - Odd polynomials act on signed eigenvalues (Hermitian case).

These constraints ensure that the transformed operator remains compatible with a unitary embedding.

All polynomials used in the notebooks ($x^2$, $x$, $x^3$, $T_3(x)$) satisfy these conditions.

---

## 4. Scalar case and QSP

When the “matrix” $A$ is just a **scalar** $x \in [-1,1]$, QSVT reduces to **Quantum Signal Processing (QSP)**.

### QSP viewpoint

In QSP:
- One considers a single “signal” unitary $U(x)$ whose eigenphases encode $x$.
- By interleaving $U(x)$ with fixed phase rotations, one constructs a circuit whose output is a polynomial function of $x$.

Thus:
$$
\text{QSVT on a scalar} \;\equiv\; \text{QSP}.
$$

Notebook 03 demonstrates this equivalence in two ways:
- using `qml.qsvt` directly on a scalar,
- and by manually constructing a single-qubit circuit whose amplitudes implement a Chebyshev polynomial.

---

## 5. Chebyshev polynomials and boundedness

Chebyshev polynomials of the first kind,
$$
T_n(x) = \cos(n \arccos x),
$$
play a central role in QSP/QSVT because:

- They satisfy $ |T_n(x)| \le 1 $ on $x \in [-1,1]$.
- They have definite parity (odd or even).
- They are optimal (minimax) approximators for many functions on bounded intervals.

For example:
$$
T_3(x) = 4x^3 - 3x,
$$
which is:
- odd,
- bounded on $[-1,1]$,
- and therefore directly admissible for QSVT.

These properties explain why Chebyshev polynomials naturally appear in polynomial constructions for matrix inversion, filtering, and Hamiltonian simulation.

---

## 6. Singular value filtering

Applying QSVT with an even polynomial $f(x)$ yields a **singular value filter**.

Example:
$$
f(x) = x^2.
$$

Effects:
- Large singular values remain relatively large.
- Small singular values are strongly suppressed.
- The transformation is smooth (not a sharp step).

This is demonstrated in Notebook 02 and illustrates how QSVT can implement *soft thresholding* without violating boundedness constraints.

---

## 7. Linear systems via polynomial inversion

Solving a linear system
$$
A x = b
$$
formally requires applying $A^{-1}$ to $|b\rangle$.

### Polynomial approximation of $1/x$

Since $1/x$ is unbounded near zero, it cannot be implemented directly by QSVT. Instead:

- One constructs a bounded polynomial $P(x)$ such that
  $$
  P(\lambda_i) \propto \frac{1}{\lambda_i}
  $$
  on the *actual eigenvalues* of $A$.

After applying $P(A)$, the resulting vector is **renormalized**, so only the *relative scaling* of eigencomponents matters.

### Special cases in the notebooks

- **Exact inverse (Notebooks 04)**  
  For involutory matrices with eigenvalues $\pm 1$:
  $$
  A^{-1} = A,
  $$
  and the polynomial $P(x)=x$ exactly reproduces the inverse.

- **Approximate inverse (Notebook 05)**  
  For eigenvalues $\pm 0.5$, the Chebyshev polynomial $T_3(x)$ satisfies:
  $$
  T_3(\lambda_i) \propto \frac{1}{\lambda_i},
  $$
  yielding the correct solution *direction* after normalization.

These examples isolate the **QSVT polynomial mechanism** without introducing additional algorithmic components.

---

## 8. What is not included

This repository deliberately omits several aspects of full-scale QSVT-based algorithms:

- State preparation and loading costs
- Success probabilities and postselection
- Amplitude amplification
- Condition number dependence
- Fault-tolerant resource estimates

The goal is conceptual clarity: understanding how **polynomial transformations of spectra** are implemented quantumly.

---

## 9. Summary

QSVT provides a unifying framework in which:

- quantum circuits implement **bounded polynomials**,
- these polynomials act on **singular values or eigenvalues**,
- QSP appears as the scalar limit,
- Chebyshev polynomials emerge naturally,
- and linear-system–like behaviour arises from inverse-approximating polynomials.

The notebooks in this repository are concrete realizations of these ideas in their simplest possible forms.

---

## References and further reading

1. Gilyén, A., Su, Y., Low, G. H., & Wiebe, N.  
   *Quantum singular value transformation and beyond: exponential improvements for quantum matrix arithmetics.*  
   arXiv:1806.01838

2. Low, G. H., & Chuang, I. L.  
   *Hamiltonian simulation by qubitization.*  
   Quantum 3, 163 (2019).

3. Low, G. H., & Chuang, I. L.  
   *Optimal Hamiltonian simulation by quantum signal processing.*  
   Physical Review Letters 118, 010501 (2017).

4. PennyLane documentation:  
   *Introduction to Quantum Singular Value Transformation (QSVT).*  
   https://pennylane.ai/qml/demos/tutorial_intro_qsvt

---

Author: Sid Richards (SidRichardsQuantum)

LinkedIn: [https://www.linkedin.com/in/sid-richards-21374b30b/](https://www.linkedin.com/in/sid-richards-21374b30b/)

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
