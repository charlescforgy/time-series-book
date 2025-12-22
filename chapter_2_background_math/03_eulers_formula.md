# Euler's Formula

## The "Jewel" of Mathematics

Euler's formula, called "our jewel" by Richard Feynman, establishes a profound connection between trigonometry and complex exponentiation.

**Euler's Formula:**

$$e^{i\theta} = \cos(\theta) + i \sin(\theta)$$

Or, multiplying through by $r$ for magnitudes different than 1:

$$r e^{i\theta} = r \cos(\theta) + i r \sin(\theta)$$

```{figure} images/complex2polar.svg
---
width: 80%
name: complex-to-polar
---
Conversion between polar and Cartesian forms of complex numbers (Charles Forgy, PhD).
```
## Euler's Identity

When $\theta=\pi$, we have:

$$e^{i\pi} = -1 + i \cdot 0$$

or

$$e^{i\pi} + 1 = 0$$

This is known as **Euler's identity**, which relates five fundamental mathematical constants: $e$, $i$, $\pi$, 1, and 0.

## Visual Demonstration


```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from IPython.display import HTML

# Create figure for Euler's formula visualization
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(14, 6))

# Left plot: Unit circle with complex exponential
theta_vals = np.linspace(0, 2*np.pi, 100)
ax1.plot(np.cos(theta_vals), np.sin(theta_vals), 'b-', linewidth=2, label='Unit circle')
ax1.set_xlim(-1.5, 1.5)
ax1.set_ylim(-1.5, 1.5)
ax1.set_aspect('equal')
ax1.grid(True, alpha=0.3)
ax1.axhline(y=0, color='k', linewidth=0.5)
ax1.axvline(x=0, color='k', linewidth=0.5)
ax1.set_xlabel('Real', fontsize=12)
ax1.set_ylabel('Imaginary', fontsize=12)
ax1.set_title('$e^{i\\theta}$ on the Complex Plane', fontsize=14)

# Demonstrate specific values
special_angles = [0, np.pi/4, np.pi/2, 3*np.pi/4, np.pi, 5*np.pi/4, 3*np.pi/2, 7*np.pi/4]
special_labels = ['0', 'π/4', 'π/2', '3π/4', 'π', '5π/4', '3π/2', '7π/4']

for theta, label in zip(special_angles, special_labels):
    x = np.cos(theta)
    y = np.sin(theta)
    ax1.plot(x, y, 'ro', markersize=8)
    ax1.plot([0, x], [0, y], 'r--', alpha=0.5, linewidth=1)
    
ax1.legend(fontsize=10)

# Right plot: Real and imaginary components
theta_range = np.linspace(0, 2*np.pi, 200)
ax2.plot(theta_range, np.cos(theta_range), 'b-', linewidth=2, label='$\\cos(\\theta) = \\Re(e^{i\\theta})$')
ax2.plot(theta_range, np.sin(theta_range), 'r-', linewidth=2, label='$\\sin(\\theta) = \\Im(e^{i\\theta})$')
ax2.axhline(y=0, color='k', linewidth=0.5)
ax2.grid(True, alpha=0.3)
ax2.set_xlabel('$\\theta$ (radians)', fontsize=12)
ax2.set_ylabel('Value', fontsize=12)
ax2.set_title('Components of $e^{i\\theta}$', fontsize=14)
ax2.set_xticks([0, np.pi/2, np.pi, 3*np.pi/2, 2*np.pi])
ax2.set_xticklabels(['0', 'π/2', 'π', '3π/2', '2π'])
ax2.legend(fontsize=10)

plt.tight_layout()
plt.show()
```

## Proof of Euler's Formula

Let $f(\theta) = \frac{\cos(\theta) + i \sin(\theta)}{e^{i\theta}}$

\begin{align}
\frac{d f(\theta)}{d\theta} &= \frac{d}{d\theta} \frac{\cos(\theta) + i \sin(\theta)}{e^{i\theta}}\\
&= \frac{d}{d\theta} e^{-i \theta}(\cos(\theta) + i \sin(\theta))\\
&= -i e^{-i \theta}(\cos(\theta) + i \sin(\theta)) + e^{-i \theta}(-\sin(\theta) + i \cos(\theta))\\
&= e^{-i \theta}(-i\cos(\theta) + \sin(\theta) - \sin(\theta) + i \cos(\theta))\\
&= e^{-i \theta}(0) = 0
\end{align}

Since $\frac{df}{d\theta} = 0$, $f(\theta)$ is constant, and we need only establish its value at a single point.

At $\theta = 0$:

$$f(0) = \frac{1 + 0i}{1} = 1$$

Therefore $f(\theta) = 1$ for all $\theta$, or:

$$e^{i\theta} = \cos(\theta) + i \sin(\theta)$$

```{note}
This formula can also be proven by replacing the exponential and trigonometric functions with their Taylor series expansions and grouping the real and complex terms.
```

## Numerical Verification


```python
# Verify Euler's formula numerically for various angles
test_angles = [0, np.pi/6, np.pi/4, np.pi/3, np.pi/2, np.pi, 2*np.pi]

print("Verifying Euler's Formula: e^(iθ) = cos(θ) + i·sin(θ)\n")
print(f"{'θ':>8} {'e^(iθ)':>25} {'cos(θ) + i·sin(θ)':>25} {'Difference':>15}")
print("-" * 80)

for theta in test_angles:
    # Calculate using exponential
    exp_result = np.exp(1j * theta)
    
    # Calculate using trigonometric form
    trig_result = np.cos(theta) + 1j * np.sin(theta)
    
    # Calculate difference
    diff = np.abs(exp_result - trig_result)
    
    # Format angle nicely
    if theta == 0:
        angle_str = "0"
    elif theta == np.pi:
        angle_str = "π"
    elif theta == 2*np.pi:
        angle_str = "2π"
    else:
        angle_str = f"{theta:.4f}"
    
    print(f"{angle_str:>8} {str(exp_result):>25} {str(trig_result):>25} {diff:>15.2e}")

print("\nAll differences are within machine precision!")
```

## Applications to Trigonometry

Use of Euler's formula can simplify trigonometric derivations by transforming geometric problems to algebraic ones. We'll explore this with the sine and cosine addition theorems in the next section.

---

**Next:** Addition Theorems for Sine and Cosine
