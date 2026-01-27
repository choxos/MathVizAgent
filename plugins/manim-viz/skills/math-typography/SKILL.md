---
name: math-typography
description: Mathematical rendering with MathTex, Tex, tex_to_color_map, custom equation classes, and formula animation patterns.
---

# Math Typography

Mathematical typography patterns for Manim animations.

## MathTex vs Tex

### MathTex - Mathematical Expressions

```python
# Single equation (entire string is math mode)
eq = MathTex(r"E = mc^2")

# Multiple parts for animation control
eq = MathTex(r"E", r"=", r"m", r"c^2")
# eq[0] = "E", eq[1] = "=", eq[2] = "m", eq[3] = "c^2"
```

### Tex - Mixed Text and Math

```python
# Text with inline math
label = Tex(r"The equation $E = mc^2$ is famous")

# Multiple parts
label = Tex(r"The value is ", r"$x = 5$")
label[1].set_color(BLUE)  # Color the math part
```

## Semantic Coloring

### tex_to_color_map

```python
# Automatic coloring of matched substrings
formula = MathTex(
    r"P(A|B) = \frac{P(B|A) \cdot P(A)}{P(B)}",
    tex_to_color_map={
        r"A": BLUE,
        r"B": ORANGE,
        r"P": GREEN,
    }
)
```

### Manual Coloring

```python
# Color specific parts
eq = MathTex(r"y", r"=", r"m", r"x", r"+", r"b")
eq[0].set_color(BLUE)    # y
eq[2].set_color(RED)     # m
eq[3].set_color(GREEN)   # x
eq[5].set_color(PURPLE)  # b
```

### Color by Index

```python
# Access characters within a part
eq = MathTex(r"f(x) = x^2")
eq[0][0].set_color(BLUE)     # 'f'
eq[0][2].set_color(GREEN)    # 'x' in f(x)
```

## Split Strategies for Animation

### Simple Split

```python
# Each term separate
eq = MathTex(r"a", r"+", r"b", r"=", r"c")

# Animate part by part
self.play(Write(eq[0]))     # a
self.play(Write(eq[1:3]))   # + b
self.play(Write(eq[3:]))    # = c
```

### Grouped Split

```python
# Logical groupings
eq = MathTex(r"y = ", r"mx", r" + ", r"b")

self.play(Write(eq[0]))   # y =
self.play(Write(eq[1]))   # mx
self.play(Write(eq[2:]))  # + b
```

### Complex Formula Split

```python
# Fraction with separate parts
frac = MathTex(
    r"\frac{",           # 0: opening
    r"a + b",            # 1: numerator
    r"}{",               # 2: middle
    r"c",                # 3: denominator
    r"}"                 # 4: closing
)
```

## Common LaTeX Patterns

### Fractions

```python
MathTex(r"\frac{numerator}{denominator}")
MathTex(r"\frac{1}{2}")
MathTex(r"\frac{x^2 + 1}{x - 1}")
```

### Greek Letters

```python
MathTex(r"\alpha, \beta, \gamma")
MathTex(r"\mu, \sigma, \theta")
MathTex(r"\Delta, \Omega, \Pi")
```

### Subscripts and Superscripts

```python
MathTex(r"x_1, x_2, x_n")
MathTex(r"x^2, x^{10}, x^{n+1}")
MathTex(r"x_i^{(k)}")  # Both
```

### Sums and Products

```python
MathTex(r"\sum_{i=1}^{n} x_i")
MathTex(r"\prod_{i=1}^{n} x_i")
MathTex(r"\sum_{x \in S} f(x)")
```

### Integrals

```python
MathTex(r"\int_{a}^{b} f(x) \, dx")
MathTex(r"\int\int_D f(x,y) \, dA")
MathTex(r"\oint_C \vec{F} \cdot d\vec{r}")
```

### Matrices

```python
MathTex(r"\begin{bmatrix} a & b \\ c & d \end{bmatrix}")
MathTex(r"\begin{pmatrix} 1 \\ 2 \\ 3 \end{pmatrix}")
MathTex(r"\begin{vmatrix} a & b \\ c & d \end{vmatrix}")
```

### Aligned Equations

```python
MathTex(r"""
\begin{aligned}
f(x) &= x^2 + 2x + 1 \\
     &= (x + 1)^2
\end{aligned}
""")
```

### Cases

```python
MathTex(r"""
f(x) = \begin{cases}
x^2 & x \geq 0 \\
-x^2 & x < 0
\end{cases}
""")
```

## Custom MathTex Classes

### Pattern: Structured Formula

```python
class QuadraticFormula(MathTex):
    """Quadratic formula with accessible parts"""

    def __init__(self, **kwargs):
        super().__init__(
            r"x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}",
            **kwargs
        )

        # Store references to parts
        self.x = self[0][0]          # x
        self.equals = self[0][1]     # =
        self.negative_b = self[0][2:4]  # -b
        self.discriminant = self[0][6:13]  # b^2 - 4ac
        self.denominator = self[0][14:16]  # 2a
```

### Pattern: Parameterized Formula

```python
class BinomialCoeff(MathTex):
    """Binomial coefficient with custom n and k"""

    def __init__(self, n, k, **kwargs):
        super().__init__(
            r"\binom{" + str(n) + r"}{" + str(k) + r"}",
            **kwargs
        )
        self.n_value = n
        self.k_value = k
```

### Pattern: Color-Coded Variables

```python
class RegressionEquation(MathTex):
    """Regression with color-coded coefficients"""

    def __init__(self, **kwargs):
        super().__init__(
            r"y", r"=", r"\beta_0", r"+", r"\beta_1", r"x", r"+", r"\epsilon",
            **kwargs
        )

        self.y = self[0].set_color(BLUE)
        self.intercept = self[2].set_color(RED)
        self.slope = self[4].set_color(GREEN)
        self.x = self[5].set_color(ORANGE)
        self.error = self[7].set_color(GRAY)
```

## Formula Animation Patterns

### Write with Timing

```python
# Simple write
self.play(Write(formula), run_time=2)
self.wait(2)
```

### Part by Part

```python
formula = MathTex(r"E", r"=", r"m", r"c^2")

for i, part in enumerate(formula):
    self.play(Write(part), run_time=0.5)
    self.wait(0.3)
```

### Transform Between Formulas

```python
eq1 = MathTex(r"a", r"+", r"b", r"=", r"c")
eq2 = MathTex(r"a", r"=", r"c", r"-", r"b")

self.play(Write(eq1))
self.wait(1)
self.play(TransformMatchingTex(eq1, eq2))
```

### Substitution

```python
# Original
eq = MathTex(r"y = ", r"x", r"^2")

# With value substituted
eq_sub = MathTex(r"y = ", r"3", r"^2")
eq_sub[1].set_color(BLUE)

self.play(Write(eq))
self.wait(1)
self.play(TransformMatchingTex(eq, eq_sub))
```

### Highlight Term

```python
formula = MathTex(r"E = mc^2")

# Temporary highlight
self.play(formula.animate.set_color(YELLOW))
self.wait(0.5)
self.play(formula.animate.set_color(WHITE))
```

## Layout and Alignment

### Centering

```python
# Default: centered on screen
formula = MathTex(r"x^2 + y^2 = r^2")
```

### Positioning

```python
formula.to_edge(UP, buff=0.5)
formula.to_corner(UL)
formula.move_to([2, 1, 0])
formula.next_to(other_object, DOWN)
```

### Multiple Equations

```python
equations = VGroup(
    MathTex(r"f(x) = x^2"),
    MathTex(r"f'(x) = 2x"),
    MathTex(r"f''(x) = 2"),
).arrange(DOWN, aligned_edge=LEFT)
```

### With Labels

```python
equation = MathTex(r"E = mc^2")
label = Tex(r"Einstein's equation")
label.next_to(equation, DOWN)

group = VGroup(equation, label)
```

## Common Statistical Formulas

```python
# Normal PDF
MathTex(r"f(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^2}{2\sigma^2}}")

# Bayes' theorem
MathTex(r"P(A|B) = \frac{P(B|A) P(A)}{P(B)}")

# Linear regression
MathTex(r"y_i = \beta_0 + \beta_1 x_i + \epsilon_i")

# Maximum likelihood
MathTex(r"\mathcal{L}(\theta) = \prod_{i=1}^n f(x_i | \theta)")

# Expected value
MathTex(r"\mathbb{E}[X] = \sum_x x \cdot P(X = x)")

# Variance
MathTex(r"\text{Var}(X) = \mathbb{E}[(X - \mu)^2]")

# Correlation
MathTex(r"\rho_{X,Y} = \frac{\text{Cov}(X,Y)}{\sigma_X \sigma_Y}")
```

## Typography Guidelines

### Font Sizes

```python
# Title formulas
formula.scale(1.2)

# Normal formulas
formula.scale(1.0)  # Default

# Annotation formulas
formula.scale(0.7)

# Or use font_size
MathTex(r"...", font_size=48)
```

### Spacing

```python
# Thin space in integrals
r"\int f(x) \, dx"

# Quad space
r"a \quad b"

# No break space
r"Figure~1"
```

### Best Practices

1. Use raw strings: `r"\frac{a}{b}"`
2. Split for animation control
3. Apply semantic colors consistently
4. Test rendering before complex scenes
5. Use custom classes for repeated formulas
