# Gradient Descent Explorer

**An interactive, browser-based visualization tool for gradient descent and stochastic gradient descent.**  
Built for *Tehnici de Optimizare — Laborator 2* (Optimization Techniques, Lab 2).

🔗 **[Live Demo]([https://your-username.github.io/your-repo-name/](https://github.com/Maximiliard5/Gradient-Descent-Visualizer/blob/main/GD_visualization.html))** ← replace with your GitHub Pages URL after setup

---

## Description

This tool lets you explore how first-order optimization algorithms behave on a variety of 2D objective functions — visually, in real time, and with full control over every hyperparameter. It was designed as a lab companion for understanding the theoretical guarantees (and failure modes) of gradient-based methods: convergence rates, the role of the condition number, step-size selection, and the effect of stochasticity.

No installation required. Everything runs in a single HTML file, entirely in the browser.

---

## What the Tool Does

### Two Optimization Modes

- **Gradient Descent (GD)** — runs the standard iterative update `xᵏ⁺¹ = xᵏ − αₖ ∇f(xᵏ)` with your choice of step-size strategy.
- **Stochastic Gradient Descent (SGD)** — replaces the full gradient with a mini-batch estimate. For quadratic objectives, a proper least-squares decomposition `f(x) = (1/m) Σᵢ ½(aᵢᵀx − bᵢ)²` is constructed so the stochastic gradients are unbiased. For non-quadratic functions, calibrated Gaussian noise is added to the true gradient.

### Step-Size Strategies (GD mode)

| Strategy | Description |
|---|---|
| **Fixed α** | Constant step size; lets you probe the divergence boundary `α > 2/L` |
| **Exact line search** | Analytically minimizes along the gradient direction (where available) |
| **Backtracking (Armijo)** | Starts from a large step and shrinks by ρ until the sufficient-decrease condition `f(x − α∇f) ≤ f(x) − cα‖∇f‖²` is satisfied |

### Objective Functions

| Function | Expression | Key property |
|---|---|---|
| **Quadratic (diagonal)** | `½(κ x₁² + x₂²)` | Condition number κ directly controls ellipse elongation and GD zig-zagging |
| **Quadratic (rotated)** | `½ xᵀ(RᵀΛR)x`, 45° rotation | Same spectrum, axis-misaligned — reveals coordinate vs. eigenvector directions |
| **Rosenbrock** | `100(x₂ − x₁²)² + (1 − x₁)²` | Narrow curved valley; GD finds it quickly but crawls along it |
| **Beale** | `Σ (cᵢ − x₁(1 − x₂ⁱ))²` | Flat regions slow gradient methods; good for comparing line-search strategies |
| **Himmelblau** | `(x₁² + x₂ − 11)² + (x₁ + x₂² − 7)²` | Four equal minima — starting point determines which one GD finds |
| **Styblinski-Tang** | `½ Σᵢ (xᵢ⁴ − 16xᵢ² + 5xᵢ)` | Multiple local minima; SGD noise can occasionally help escape shallow basins |

### Interface

- **Left panel — Contour Plot + Iterates**: shows level curves of f and the trajectory of xᵏ. Click anywhere on the plot to set a new starting point x⁰.
- **Right panel — Convergence Plot**: plots `f(xᵏ) − f*` over iterations on a log scale, giving an immediate visual of the convergence rate.
- **Sidebar**: controls for method, function, step-size strategy, all hyperparameters (α, Armijo c and ρ, mini-batch size N, step-decay τ, max iterations, condition number κ), run/step/reset buttons, and a live statistics readout (current iteration, f(xᵏ), ‖∇f(xᵏ)‖, αₖ, and convergence status).
- **Formula bar**: displays the active objective, its Lipschitz constant L, strong convexity constant σ, condition number κ, and optimum x*.
- **Info box**: context-sensitive explanation of the selected function's theoretical properties and what to look for.

---

## Suggested Experiments

1. **Condition number and convergence rate** — select the diagonal quadratic, fix α = 2/(L+σ), and sweep κ from 1 to 200. Observe how the convergence rate `((κ−1)/(κ+1))²` per iteration degrades.
2. **Divergence boundary** — push α past 2/L on the quadratic and watch the iterates explode.
3. **Backtracking robustness** — switch to Rosenbrock with a large initial step; see how Armijo automatically adapts.
4. **Initialization sensitivity** — on Himmelblau, click the four different basins and confirm GD converges to a different minimum each time.
5. **SGD noise vs. batch size** — on Styblinski-Tang in SGD mode, set batch N=1 and watch the noisy trajectory; increase N toward 50 and see variance collapse.

---

## Running Locally

```bash
# Just open the file in any modern browser
open GD_visualization.html      # macOS
xdg-open GD_visualization.html  # Linux
start GD_visualization.html     # Windows
```

No build step, no dependencies, no server required.

--

## License

MIT — free to use, modify, and share.
