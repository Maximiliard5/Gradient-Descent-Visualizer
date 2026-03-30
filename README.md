# Gradient Descent Explorer

**An interactive, browser-based visualization tool for gradient descent, stochastic gradient descent, projected gradient descent, and the conditional gradient method (Frank-Wolfe).**  
Built for *Tehnici de Optimizare* (Optimization Techniques) lab sessions.

🔗 **[Live Demo](https://maximiliard5.github.io/Gradient-Descent-Visualizer/GD_visualization.html)** ← replace with your GitHub Pages URL after setup

---

## Description

This tool lets you explore how first-order optimization algorithms behave on a variety of 2D objective functions — visually, in real time, and with full control over every hyperparameter. It was designed as a lab companion for understanding the theoretical guarantees (and failure modes) of gradient-based methods: convergence rates, the role of the condition number, step-size selection, the effect of stochasticity, and constrained optimization via both projection-based and projection-free methods.

No installation required. Everything runs in a single HTML file, entirely in the browser.

---

## What the Tool Does

### Four Optimization Modes

- **Gradient Descent (GD)** — the standard unconstrained iterative update $x^{k+1} = x^k - \alpha_k \nabla f(x^k)$ with your choice of step-size strategy.
- **Stochastic Gradient Descent (SGD)** — replaces the full gradient with a mini-batch estimate. For quadratic objectives, a proper least-squares decomposition $f(x) = \frac{1}{m} \sum_i \frac{1}{2}(a_i^\top x - b_i)^2$ is constructed so the stochastic gradients are unbiased. For non-quadratic functions, calibrated Gaussian noise is added to the true gradient.
- **Projected Gradient Descent (PGD)** — solves $\min_{x \in Q} f(x)$ by taking a gradient step and then projecting back onto the feasible set: $x^{k+1} = \Pi_Q(x^k - \alpha_k \nabla f(x^k))$. The visualization shows "ghost" dots at the pre-projection point so you can see the projection operator in action.
- **Conditional Gradient (Frank-Wolfe)** — solves $\min_{x \in Q} f(x)$ without projections. At each step, a linear minimization oracle (LMO) computes $\varphi_Q(x^k) = \arg\min_{z \in Q} \nabla f(x^k)^\top z$, then updates via the convex combination $x^{k+1} = x^k + \alpha_k(\varphi_Q(x^k) - x^k)$. Iterates are always feasible.

### Step-Size Strategies

**GD mode:**

| Strategy | Description |
|---|---|
| **Fixed $\alpha$** | Constant step size; lets you probe the divergence boundary $\alpha > 2/L$ |
| **Exact line search** | Analytically minimizes along the gradient direction (where available) |
| **Backtracking (Armijo)** | Starts from a large step and shrinks by $\rho$ until the sufficient-decrease condition $f(x - \alpha\nabla f) \leq f(x) - c\alpha\|\nabla f\|^2$ is satisfied |

**Projected GD mode:**

| Strategy | Description |
|---|---|
| **Fixed $\alpha$** | Constant step size for the gradient step before projection |
| **$\alpha = 1/L$** | The theoretically optimal step size when the Lipschitz constant $L$ is known |
| **Backtracking (Armijo + proj.)** | Armijo line search where each candidate step is projected onto $Q$ before checking the sufficient-decrease condition |

**Conditional Gradient mode:**

| Strategy | Description |
|---|---|
| **$\alpha_k = 2/(k+2)$** | The classical Frank-Wolfe step size; guarantees $O(1/k)$ convergence for convex $f$ |
| **Fixed $\alpha$** | Constant step size in $[0, 1]$ |
| **Line search on $[0, 1]$** | Minimizes $f(x^k + \alpha(\varphi_Q(x^k) - x^k))$ over $\alpha \in [0,1]$ via golden section |

### Constraint Sets (PGD and Conditional Gradient modes)

| Set | Definition | PGD projection | FW LMO solution |
|---|---|---|---|
| **$\ell_2$ ball** | $\|x\|_2 \leq r$ | $r \cdot x / \|x\|$ (rescale) | $-r \cdot \nabla f / \|\nabla f\|$ |
| **$\ell_1$ ball** | $\|x\|_1 \leq r$ | Soft-thresholding | $-r \cdot \text{sign}(g_i) \cdot e_i$ for $i = \arg\max |g_i|$ |
| **$\ell_\infty$ ball** | $\|x\|_\infty \leq r$ | Clamp each component | $-r \cdot \text{sign}(g)$ componentwise |
| **Simplex** | $x \geq 0,\; x_1+x_2 \leq r$ | Project onto simplex | Best vertex among $\{0, r e_1, r e_2\}$ |

### Objective Functions

| Function | Expression | Key property |
|---|---|---|
| **Quadratic (diagonal)** | $\frac{1}{2}(\kappa x_1^2 + x_2^2)$ | Condition number $\kappa$ directly controls ellipse elongation and GD zig-zagging |
| **Quadratic (rotated)** | $\frac{1}{2} x^\top(R^\top\Lambda R)x$, 45° rotation | Same spectrum, axis-misaligned — reveals coordinate vs. eigenvector directions |
| **Rosenbrock** | $100(x_2 - x_1^2)^2 + (1 - x_1)^2$ | Narrow curved valley; GD finds it quickly but crawls along it |
| **Beale** | $\sum (c_i - x_1(1 - x_2^i))^2$ | Flat regions slow gradient methods; good for comparing line-search strategies |
| **Himmelblau** | $(x_1^2 + x_2 - 11)^2 + (x_1 + x_2^2 - 7)^2$ | Four equal minima — starting point determines which one GD finds |
| **Styblinski-Tang** | $\frac{1}{2} \sum_i (x_i^4 - 16x_i^2 + 5x_i)$ | Multiple local minima; SGD noise can occasionally help escape shallow basins |
| **Shifted Quadratic** | $\frac{1}{2}((x_1-3)^2 + (x_2-2)^2)$ | Minimum at $(3, 2)$ — lies outside small constraint sets, ideal for comparing PGD vs FW |

### Interface

- **Left panel — Contour Plot**: shows level curves of $f$ and the trajectory of $x^k$. In constrained modes (PGD and Cond. GD), the constraint set $Q$ is drawn as a purple dashed region. PGD mode shows "ghost" dots at the pre-projection locations. Cond. GD mode shows dotted lines from each iterate to its LMO vertex. Click the plot to set a new starting point $x^0$ (must be inside $Q$ in constrained modes).
- **Right panel — Convergence Plot**: plots $f(x^k) - f^*$ over iterations on a log scale. In Cond. GD mode, the **duality gap** $g_k = \nabla f(x^k)^\top(x^k - \varphi_Q(x^k))$ is plotted as a second curve — a computable convergence certificate that doesn't require knowing $f^*$.
- **Sidebar**: controls for method, function, step-size strategy, constraint set and radius (constrained modes), all hyperparameters, run/step/reset buttons, and a live statistics readout.
- **Formula bar**: displays the active objective and key properties; in constrained modes shows the constraint set and radius.
- **Info box**: context-sensitive explanation that adapts to the selected method and function.

---

## Suggested Experiments

### Gradient Descent

1. **Condition number and convergence rate** — select the diagonal quadratic, fix $\alpha = 2/(L+\sigma)$, and sweep $\kappa$ from 1 to 200. Observe the degrading rate.
2. **Divergence boundary** — push $\alpha$ past $2/L$ and watch the iterates explode.
3. **Backtracking robustness** — switch to Rosenbrock with a large initial step; see how Armijo adapts.
4. **Initialization sensitivity** — on Himmelblau, click the four different basins.

### Stochastic Gradient Descent

5. **SGD noise vs. batch size** — on Styblinski-Tang, set batch $N=1$ and watch the noisy trajectory; increase $N$ toward 50 and see variance collapse.

### Projected Gradient Descent

6. **Visualize projection** — on the shifted quadratic with $r=2$ (so the min is outside $Q$), step through iterations and watch the dim blue "ghost" dots appear outside $Q$, then snap back to the boundary.
7. **$\alpha = 1/L$ optimality** — on the diagonal quadratic, compare fixed $\alpha$ vs $\alpha = 1/L$. The latter gives the optimal rate for smooth convex objectives.
8. **PGD vs FW convergence rates** — run PGD on the shifted quadratic, note the linear convergence rate. Switch to Cond. GD on the same problem and see the sublinear $O(1/k)$ rate. The convergence plot makes the difference immediately visible.
9. **Armijo + projection** — on Rosenbrock with a box constraint, compare fixed $\alpha$ (may diverge or crawl) vs Armijo backtracking with projection (adapts automatically).

### Conditional Gradient (Frank-Wolfe)

10. **Constraint geometry matters** — on the diagonal quadratic ($\kappa=10$), compare the $\ell_1$ ball (FW jumps between axis-aligned vertices) vs the $\ell_2$ ball (smooth boundary tracking).
11. **Sublinear convergence** — run FW with $\alpha_k = 2/(k+2)$ and observe the $O(1/k)$ rate.
12. **Duality gap as stopping criterion** — step through iterations one at a time and watch the duality gap decrease.
13. **Simplex sparsity** — select the simplex constraint; the LMO always returns a vertex, so iterates are convex combinations of extreme points.

---

## Running Locally

```bash
# Just open the file in any modern browser
open GD_visualization.html      # macOS
xdg-open GD_visualization.html  # Linux
start GD_visualization.html     # Windows
```

No build step, no dependencies, no server required.
