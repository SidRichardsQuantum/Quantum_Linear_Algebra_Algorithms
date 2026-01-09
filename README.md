# Quantum Singular Value Transformation (QSVT)

This repository is a notebook-first, PennyLane-based introduction to **Quantum Singular Value Transformation (QSVT)** and its close relationship to **Quantum Signal Processing (QSP)**. The focus is on small, explicit examples that show—end to end—how **bounded polynomials** can be applied to the **singular values / eigenvalues** of an operator via a (simulator-friendly) block-encoding.

The notebooks use PennyLane’s high-level `qml.qsvt` interface (with `block_encoding="embedding"`) to keep the emphasis on the mathematical mechanism and the resulting transformed spectra.

## What is QSVT

QSVT is a framework for implementing **polynomial transformations** of an operator’s singular values using a quantum circuit, provided the operator is available via a **block-encoding**.
Concretely: given a block-encoding of a matrix $A$, QSVT enables a circuit whose action corresponds (in the encoded subspace) to applying a polynomial $f$ so that singular values $\sigma_i$ are mapped to $f(\sigma_i)$, subject to standard QSVT admissibility constraints such as boundedness on $[-1,1]$ and parity structure.

For a more extensive theory - see [THEORY.md](THEORY.md)

## Repository structure

```
Quantum_Singular_Value_Transformation
├── LICENSE
├── README.md
└── notebooks
    ├── 01_QSVT_Scalar_and_Diagonal_Matrix.ipynb
    ├── 02_QSVT_Singular_Value_Filter.ipynb
    ├── 03_QSP_Polynomial_Demo.ipynb
    ├── 04_QSVT_Linear_Solver_2x2.ipynb
    ├── 04_QSVT_Linear_Solver_4x4.ipynb
    └── 05_QSVT_Linear_Solver_Approximate.ipynb
```

## Suggested reading order

The notebooks are designed to be read sequentially:

### 01 — QSVT on a scalar and a diagonal matrix
**`01_QSVT_Scalar_and_Diagonal_Matrix.ipynb`**

- Minimal “single singular value” intuition: treat a scalar $a \in [-1,1]$ as a block-encoded operator and apply a polynomial.
- Applies the polynomial $f(x)=x^2$ and verifies the QSVT output matches the classical curve.
- Extends the same idea to a **diagonal matrix**, illustrating that QSVT transforms eigenvalues/singular values **independently**.

### 02 — QSVT as a (soft) singular value filter
**`02_QSVT_Singular_Value_Filter.ipynb`**

- Demonstrates a simple *filtering / suppression* viewpoint by applying a bounded even polynomial (again $f(x)=x^2$).
- Shows how smaller singular values are attenuated more strongly than larger ones.
- Highlights the key admissibility condition used throughout QSVT: $ |f(x)| \le 1 $ for $x \in [-1,1]$.

### 03 — QSP demo (two perspectives)
**`03_QSP_Polynomial_Demo.ipynb`**

- **Part A:** QSP via QSVT-on-a-scalar (QSVT reduces to QSP in the scalar case).
- **Part B:** a manual, single-qubit “QSP-style” construction showing how repeating a signal unitary yields a **Chebyshev polynomial**:
  $$
  T_3(x) = 4x^3 - 3x.
  $$
- Motivates why Chebyshev polynomials appear naturally in this area (boundedness and approximation properties on $[-1,1]$).

### 04 — QSVT linear solver (exact inverse on a special spectrum)
**`04_QSVT_Linear_Solver_2x2.ipynb`** and **`04_QSVT_Linear_Solver_4x4.ipynb`**

- Constructs “linear solver” demonstrations using a **special class of Hermitian involutory matrices** with eigenvalues $\pm 1$.
- For these matrices, $A^2 = I$ and therefore $A^{-1} = A$.
- Using the polynomial $P(x)=x$ (bounded and odd), QSVT exactly reproduces the inverse on this spectrum—making these examples fully correct but intentionally “spectrally trivial.”

### 05 — QSVT linear solver (approximate / inverse-like polynomial)
**`05_QSVT_Linear_Solver_Approximate.ipynb`**

- A non-trivial 2×2 example where $A \ne A^{-1}$.
- Uses the bounded odd Chebyshev polynomial $T_3(x)=4x^3-3x$ as an **inverse-like** transformation on the chosen eigenvalues.
- Demonstrates a key conceptual point: for linear-system-style workflows, matching the **relative eigencomponent scaling** can be sufficient when the output is **renormalized** (i.e., solution direction is compared).

## Scope and limitations (important)

This repository is intended as an educational / conceptual demonstration.

- The notebooks use PennyLane’s convenient `block_encoding="embedding"` mode, which is well-suited for small matrices and simulators.
- The “linear solver” notebooks illustrate the **QSVT polynomial mechanism** behind inversion-like transformations. They do not implement a full end-to-end, large-scale algorithmic stack (e.g., state preparation, success probability management, amplitude amplification, condition-number scaling, or fault-tolerant resource accounting).
- The examples focus on clarity and correctness for small dimensions rather than performance or generality.

## Requirements

- Python 3.10+ (recommended)
- PennyLane
- NumPy
- Matplotlib
- Jupyter (or JupyterLab)

## Quickstart

1. Clone the repository:

   ```bash
   git clone https://github.com/SidRichardsQuantum/Quantum_Singular_Value_Transformation.git
   cd Quantum_Singular_Value_Transformation
   ```

2. Create and activate an environment (example with `venv`):

   ```bash
   python -m venv .venv
   source .venv/bin/activate  # macOS/Linux
   # .venv\Scripts\activate   # Windows PowerShell
   ```

3. Install dependencies:

   ```bash
   pip install pennylane numpy matplotlib jupyter
   ```

4. Launch Jupyter:

   ```bash
   jupyter lab
   ```

   Then open the notebooks in `notebooks/` in numerical order.

---

Author: Sid Richards (SidRichardsQuantum)

LinkedIn: [https://www.linkedin.com/in/sid-richards-21374b30b/](https://www.linkedin.com/in/sid-richards-21374b30b/)

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
