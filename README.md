# Gradient Descent Explorer

**An interactive, browser-based visualization tool for gradient descent, stochastic gradient descent, and the conditional gradient method (Frank-Wolfe).**  
Built for *Tehnici de Optimizare* (Optimization Techniques) lab sessions.

🔗 **[Live Demo](https://maximiliard5.github.io/Gradient-Descent-Visualizer/GD_visualization.html)** ← replace with your GitHub Pages URL after setup

---

## Description

This tool lets you explore how first-order optimization algorithms behave on a variety of 2D objective functions — visually, in real time, and with full control over every hyperparameter. It was designed as a lab companion for understanding the theoretical guarantees (and failure modes) of gradient-based methods: convergence rates, the role of the condition number, step-size selection, the effect of stochasticity, and constrained optimization via projection-free methods.

No installation required. Everything runs in a single HTML file, entirely in the browser.

---

## What the Tool Does

### Three Optimization Modes

- **Gradient Descent (GD)** — runs the standard iterative update $xᵏ⁺¹ = xᵏ − αₖ ∇f(xᵏ)$ with your choice of step-size strategy.
- **Stochastic Gradient Descent (SGD)** — replaces the full gradient with a mini-batch estimate. For quadratic objectives, a proper least-squares decomposition $f(x) = (1/m) Σᵢ ½(aᵢᵀx − bᵢ)²$ is constructed so the stochastic gradients are unbiased. For non-quadratic functions, calibrated Gaussian noise is added to the true gradient.
- **Conditional Gradient (Frank-Wolfe)** — solves $min f(x) s.t. x ∈ Q$ without projections. At each step, a linear minimization oracle (LMO) computes $φ_Q(xᵏ) = argmin_{z∈Q} ∇f(xᵏ)ᵀz$, then updates via the convex combination $xᵏ⁺¹ = xᵏ + αₖ(φ_Q(xᵏ) − xᵏ)$. Iterates are always feasible.

### Step-Size Strategies

**GD mode:**

| Strategy | Description |
|---|---|
| **Fixed α** | Constant step size; lets you probe the divergence boundary $α > 2/L$ |
| **Exact line search** | Analytically minimizes along the gradient direction (where available) |
| **Backtracking (Armijo)** | Starts from a large step and shrinks by ρ until the sufficient-decrease condition $f(x − α∇f) ≤ f(x) − cα‖∇f‖²$ is satisfied |

**Conditional Gradient mode:**

| Strategy | Description |
|---|---|
| **αₖ = 2/(k+2)** | The classical Frank-Wolfe step size; guarantees O(1/k) convergence for convex f |
| **Fixed α** | Constant step size in [0, 1] |
| **Line search on [0, 1]** | Minimizes $f(xᵏ + α(φ_Q(xᵏ) − xᵏ))$ over α ∈ [0, 1] via golden section |

### Constraint Sets (Conditional Gradient mode)

| Set | Definition | LMO solution |
|---|---|---|
| **L₂ ball** | `‖x‖₂ ≤ r` | `−r · ∇f / ‖∇f‖` (normalized negative gradient) |
| **L₁ ball (diamond)** | `‖x‖₁ ≤ r` | `−r · sign(gᵢ) · eᵢ` for `i = argmax \|gᵢ\|` — always a vertex, promotes sparsity |
| **L∞ ball (box)** | `‖x‖∞ ≤ r` | `−r · sign(g)` componentwise — always a corner |
| **Simplex** | `x ≥ 0, x₁+x₂ ≤ r` | Best vertex among `{0, r·e₁, r·e₂}` — extreme sparsity |

### Objective Functions

| Function | Expression | Key property |
|---|---|---|
| **Quadratic (diagonal)** | `½(κ x₁² + x₂²)` | Condition number κ directly controls ellipse elongation and GD zig-zagging |
| **Quadratic (rotated)** | `½ xᵀ(RᵀΛR)x`, 45° rotation | Same spectrum, axis-misaligned — reveals coordinate vs. eigenvector directions |
| **Rosenbrock** | `100(x₂ − x₁²)² + (1 − x₁)²` | Narrow curved valley; GD finds it quickly but crawls along it |
| **Beale** | `Σ (cᵢ − x₁(1 − x₂ⁱ))²` | Flat regions slow gradient methods; good for comparing line-search strategies |
| **Himmelblau** | `(x₁² + x₂ − 11)² + (x₁ + x₂² − 7)²` | Four equal minima — starting point determines which one GD finds |
| **Styblinski-Tang** | `½ Σᵢ (xᵢ⁴ − 16xᵢ² + 5xᵢ)` | Multiple local minima; SGD noise can occasionally help escape shallow basins |
| **Shifted Quadratic** | `½((x₁−3)² + (x₂−2)²)` | Minimum at (3, 2) — lies outside small constraint sets, ideal for demonstrating constrained optimization |

### Interface

- **Left panel — Contour Plot**: shows level curves of f and the trajectory of xᵏ. In Conditional Gradient mode, the constraint set Q is drawn as a purple dashed region, and dotted lines show the FW direction from each iterate to its LMO vertex. Click the plot to set a new starting point x⁰ (must be inside Q in Conditional Gradient mode).
- **Right panel — Convergence Plot**: plots `f(xᵏ) − f*` over iterations on a log scale. In Conditional Gradient mode, the **duality gap** `gₖ = ∇f(xᵏ)ᵀ(xᵏ − φ_Q(xᵏ))` is plotted as a second curve — this is a computable convergence certificate that doesn't require knowing f*.
- **Sidebar**: controls for method, function, step-size strategy, constraint set and radius (Conditional Gradient mode), all hyperparameters, run/step/reset buttons, and a live statistics readout (current iteration, f(xᵏ), ‖∇f(xᵏ)‖, duality gap, αₖ, and convergence status).
- **Formula bar**: displays the active objective, its key properties, and (in Conditional Gradient mode) the constraint set and radius.
- **Info box**: context-sensitive explanation that adapts its content to the selected method and function.

---

## Suggested Experiments

### Gradient Descent

1. **Condition number and convergence rate** — select the diagonal quadratic, fix α = 2/(L+σ), and sweep κ from 1 to 200. Observe how the convergence rate `((κ−1)/(κ+1))²` per iteration degrades.
2. **Divergence boundary** — push α past 2/L on the quadratic and watch the iterates explode.
3. **Backtracking robustness** — switch to Rosenbrock with a large initial step; see how Armijo automatically adapts.
4. **Initialization sensitivity** — on Himmelblau, click the four different basins and confirm GD converges to a different minimum each time.

### Stochastic Gradient Descent

5. **SGD noise vs. batch size** — on Styblinski-Tang in SGD mode, set batch N=1 and watch the noisy trajectory; increase N toward 50 and see variance collapse.

### Conditional Gradient (Frank-Wolfe)

6. **Constraint geometry matters** — on the diagonal quadratic (κ=10), compare the L₁ ball (FW jumps between axis-aligned vertices) vs. the L₂ ball (smooth boundary tracking). Notice how L₁ produces sparse iterates.
7. **Sublinear convergence** — run FW with αₖ = 2/(k+2) on the shifted quadratic with r=2. The convergence plot shows the characteristic O(1/k) rate — much slower than GD's linear rate on the unconstrained problem.
8. **Line search acceleration** — switch from the standard 2/(k+2) step to line search on [0,1] and observe the improvement, especially in early iterations.
9. **Duality gap as stopping criterion** — step through the shifted quadratic one iteration at a time and watch the duality gap (purple dashed curve) decrease. This gap upper-bounds the suboptimality without knowing f*.
10. **Simplex sparsity** — select the simplex constraint and step through iterations. The LMO always returns a vertex (one of the simplex corners), so each FW step moves toward an extreme point — the iterates are convex combinations of vertices.

---

## Running Locally

```bash
# Just open the file in any modern browser
open GD_visualization.html      # macOS
xdg-open GD_visualization.html  # Linux
start GD_visualization.html     # Windows
```

No build step, no dependencies, no server required.
