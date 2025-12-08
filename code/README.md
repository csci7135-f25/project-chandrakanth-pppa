# Neural Network Static Analysis

> **Note:** This directory contains a submodule linking to the main repository within the CUPLV organization. You can clone this repository normally, and the submodule will function as intended. The README content below mirrors the original repository.


Code for the project titled `Certifying Differential Invariants in Backpropagation of Neural Networks using Abstract Duals`. This leverages the [`ocaml-nn`](https://github.com/ck090/ocaml-nn/tree/main) library to perform static analysis, implementing and comparing **DeepPoly** domain analysis against **Gradient-based verification** (using Abstract Dual Numbers).

## Features
The main features of the implementation are the following:
- **DeepPoly Analysis**: Implements the DeepPoly abstract domain for precise layer-by-layer bound propagation.
- **Gradient Verification**: Uses Abstract Dual Numbers to compute global Lipschitz bounds for robustness certification.
- **Activation Support**: Full support for **Sigmoid** (primary focus) and **ReLU** (secondary) activations.
- **Instability Detection**: Metrics to detect gradient instability and its correlation with robustness failure.

## Getting Started
 
### Prerequisites
- **OCaml** (4.12+)
- **Dune** build system

### Installation

```bash
git clone https://github.com/cuplv/nn_static_analysis.git
cd nn_static_analysis
dune build
```

### Usage

To run the main gradient verification experiment:

```bash
dune exec ./verify_gradient.exe
```

To run the standard DeepPoly verification:

```bash
dune exec ./verify.exe
```

## Experimental Results

We conducted an extensive comparative analysis between DeepPoly and Gradient-based verification.

### 1. Gradient Instability & Robustness (Sigmoid)
**Hypothesis:** Does "Gradient Instability" (gradient interval spanning 0) predict robustness failure?
*   **Experiment:** High perturbation ($\epsilon = 0.12$) on Tiny, Small, and Standard networks.
*   **Result:** **100% Correlation**. Every robustness failure coincided with an unstable gradient.
*   **Interpretation:** At this perturbation level, the decision surface becomes highly non-monotonic, rendering linear approximations ineffective.

### 2. The "Gradient Robust, DeepPoly Unstable" Anomaly
We identified specific edge cases where the Gradient Method certifies robustness while DeepPoly fails.

| Network | Epsilon | Count | % of Total |
| :--- | :--- | :--- | :--- |
| **Tiny Net** | 0.01 | 5 | 16.1% |
| **Tiny Net** | 0.02 | 3 | 9.7% |
| **Tiny Net** | 0.03 | 2 | 6.5% |
| **Small/Standard** | All | 0 | 0.0% |

**Key Insight:** In very small, shallow networks (Tiny Net), the global gradient bound—even if imperfect—can sometimes outperform the accumulated error from DeepPoly's layer-by-layer abstraction. As network complexity increases, DeepPoly's handling of dependencies clearly outperforms the coarser global Lipschitz bound used in the Gradient method.

#### Specific Edge Cases (Tiny Net)
We found the following indices where Gradient Method > DeepPoly:

*   **$\epsilon=0.01$:** Indices `19`, `76`, `122`, `139`, `148` (All Label 8)
*   **$\epsilon=0.02$:** Indices `122`, `139`, `148`
*   **$\epsilon=0.03$:** Indices `139`, `148`

#### Deep Dive: Analysis of Case Idx 139 (Label 8)
*   **DeepPoly Status:** `UNSTABLE` (Forward Radius: `0.0000` - likely very small but non-zero overlap in abstract domain).
*   **Gradient Status:** `ROBUST` (Gradient Radius: `0.0000`).
*   **Gradient Stability:** `NO(480)` (Unstable gradient in 480 pixels).
*   **Interpretation:** Even though the gradient is unstable (non-monotonic), the *magnitude* of the gradient is small enough that the certified variation ($\epsilon \times \|\nabla f\|_1$) is smaller than the margin between the true class and the runner-up. DeepPoly, being a layer-by-layer abstraction, likely accumulated enough over-approximation error to lose the verification, whereas the Gradient method (despite the instability) found a tight enough bound on the variation.

### 3. ReLU Activation Analysis
The activation was switched to ReLU to compare method performance.

*   **DeepPoly Dominance:** Tighter or equal bounds in **100%** of cases.
*   **Gradient Degradation:** The Gradient method performed significantly worse here than with Sigmoid.
*   **Reasoning:** The ReLU derivative is discontinuous ($\{0, 1\}$). The abstract transformer must treat unstable neurons as the full 1$ interval. This lack of smoothness creates much looser Lipschitz bounds compared to the smooth Sigmoid derivative.

## Project Structure

*   `src/`: Core implementation of abstract domains.
    *   `deeppoly.ml`: DeepPoly domain logic.
    *   `abstract_dual.ml`: Abstract Dual Numbers for gradient computation.
    *   `neuralnet.ml`: Neural network training and inference.
*   `data/`: CSV datasets for training and testing.
*   `verify_gradient.ml`: Main driver for comparative experiments.
*   `verify.ml`: Driver for standard DeepPoly verification.

Rest of the code is the same as that of the original `ocaml-nn` library

## Conclusion

1.  **DeepPoly** is the gold standard for general verification, consistently providing tighter bounds and higher certification rates.
2.  The **Gradient Method** has a specific niche in **very small, smooth (Sigmoid) networks** at low perturbation.
3.  **Gradient Instability** is a powerful diagnostic tool for predicting robustness failure at high perturbation levels. It is a useful diagnostic for spotting when local linearity assumptions are collapsing.

Definitely worth exploring in the future.

Sample output

![Sample output](image.png)

---
*Written by Chandra Kanth Nagesh as part of the course CSCI-7135*
