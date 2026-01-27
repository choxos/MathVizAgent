---
name: animation-patterns
description: Production animation patterns including reveal, transform, progressive reveal, emphasis, and cleanup patterns.
---

# Animation Patterns

Production-quality animation patterns for mathematical visualizations.

## Reveal Patterns

### Simple Reveal

```python
# Text/formulas
self.play(Write(text), run_time=1.5)
self.wait(1)

# Shapes
self.play(Create(shape), run_time=1)

# With fill
self.play(DrawBorderThenFill(shape), run_time=1.5)
```

### Dramatic Entrance

```python
# Grow from center
self.play(GrowFromCenter(obj), run_time=1.5)

# Grow from edge
self.play(GrowFromEdge(obj, LEFT), run_time=1)

# Spin in
self.play(SpinInFromNothing(obj), run_time=1.5)
```

### Fade Variants

```python
# Simple fade
self.play(FadeIn(obj))

# With direction
self.play(FadeIn(obj, shift=UP))
self.play(FadeIn(obj, shift=DOWN * 0.5))

# Scale fade
self.play(FadeIn(obj, scale=0.5))  # Grow while fading in
```

## Transform Patterns

### Basic Transform

```python
# Morph A into B (A still exists, looks like B)
self.play(Transform(a, b))

# Replace A with B (A is removed, B is added)
self.play(ReplacementTransform(a, b))

# Cross-fade
self.play(FadeTransform(a, b))
```

### Matching Transforms

```python
# Match shapes between mobjects
self.play(TransformMatchingShapes(text1, text2))

# Match TeX parts
eq1 = MathTex("a", "+", "b", "=", "c")
eq2 = MathTex("a", "=", "c", "-", "b")
self.play(TransformMatchingTex(eq1, eq2))
```

### Staged Transform

```python
# Transform in stages
self.play(a.animate.move_to(b.get_center()))
self.play(Transform(a, b))

# Or animate properties first
self.play(a.animate.set_color(b.get_color()))
self.play(Transform(a, b))
```

## Progressive Reveal Patterns

### LaggedStart

```python
# Stagger multiple animations
elements = [Circle(), Square(), Triangle()]

self.play(LaggedStart(
    *[Create(e) for e in elements],
    lag_ratio=0.3  # 30% overlap
), run_time=3)
```

### LaggedStartMap

```python
# Cleaner syntax for same animation type
group = VGroup(Circle(), Square(), Triangle())

self.play(LaggedStartMap(
    Create,           # Animation class
    group,            # Target group
    lag_ratio=0.2
), run_time=2)

# With extra parameters
self.play(LaggedStartMap(
    FadeIn,
    group,
    lag_ratio=0.1,
    shift=UP * 0.5    # Each element fades in from below
), run_time=2)
```

### Progressive Data Points

```python
# Add points progressively
points = VGroup(*[Dot(axes.c2p(x, y)) for x, y in data])

# Fast progressive reveal
self.play(LaggedStart(
    *[FadeIn(p, scale=0.5) for p in points],
    lag_ratio=0.02
), run_time=3)
```

### Sequential Section Reveal

```python
# Reveal formula parts
formula = MathTex("y", "=", "m", "x", "+", "b")

self.play(Write(formula[0:2]))  # "y ="
self.wait(0.5)
self.play(Write(formula[2:4]))  # "mx"
self.wait(0.5)
self.play(Write(formula[4:]))   # "+ b"
```

## Emphasis Patterns

### Indicate

```python
# Temporary highlight
self.play(Indicate(obj))
self.play(Indicate(obj, color=YELLOW))
self.play(Indicate(obj, scale_factor=1.2))
```

### Circumscribe

```python
# Draw outline around object
self.play(Circumscribe(obj))
self.play(Circumscribe(obj, color=RED))
self.play(Circumscribe(obj, shape=Rectangle))
```

### Flash

```python
# Brief flash effect
self.play(Flash(obj))
self.play(Flash(obj, color=YELLOW, flash_radius=0.5))
```

### FocusOn

```python
# Draw attention with pulse
self.play(FocusOn(obj))
self.play(FocusOn(point))
```

### Wiggle

```python
# Shake object
self.play(Wiggle(obj))
```

### Combined Emphasis

```python
# For critical insights
def emphasize(self, obj):
    self.play(Indicate(obj, color=YELLOW))
    self.play(Circumscribe(obj, color=YELLOW))
    self.play(Flash(obj))
    self.wait(2)
```

## Cleanup Patterns

### Explicit Tracking

```python
class MyScene(Scene):
    def setup(self):
        self.tracked = []

    def track(self, *mobjects):
        for m in mobjects:
            self.tracked.append(m)
            self.add(m)
        return mobjects[0] if len(mobjects) == 1 else mobjects

    def cleanup(self, keep=None):
        keep = keep or []
        to_remove = [m for m in self.tracked if m not in keep]
        if to_remove:
            self.play(*[FadeOut(m) for m in to_remove])
        self.tracked = list(keep)
```

### Group Cleanup

```python
# Group related objects for easy cleanup
section1_objects = VGroup(title, subtitle, diagram)
self.play(FadeOut(section1_objects))
```

### Transition Pattern

```python
def transition_to_next_section(self, keep=None):
    """Fade out current section, prepare for next"""
    keep = keep or []

    # Fade out everything except kept objects
    to_remove = [m for m in self.mobjects if m not in keep]
    if to_remove:
        self.play(*[FadeOut(m) for m in to_remove], run_time=0.5)

    self.wait(0.5)
```

## Dynamic Update Patterns

### ValueTracker with always_redraw

```python
# Create tracker
tracker = ValueTracker(0)

# Create dynamic object
dot = always_redraw(
    lambda: Dot(axes.c2p(tracker.get_value(), f(tracker.get_value())))
)

self.add(dot)

# Animate the tracker
self.play(tracker.animate.set_value(5), run_time=3)
```

### Manual Updater

```python
# Add updater function
def update_label(label):
    label.next_to(dot, UP)

label.add_updater(update_label)
self.add(label)

# Remove when done
label.clear_updaters()
```

### Dynamic Area

```python
tracker = ValueTracker(-3)

area = always_redraw(
    lambda: axes.get_area(
        graph,
        x_range=[-3, tracker.get_value()],
        color=BLUE
    )
)

self.add(area)
self.play(tracker.animate.set_value(3), run_time=4)
```

## Composition Patterns

### Simultaneous

```python
# Multiple animations at once
self.play(
    Create(circle),
    Write(label),
    FadeIn(background)
)
```

### Succession

```python
# Sequential in single play call
self.play(Succession(
    Create(circle),
    Wait(0.5),
    circle.animate.set_color(RED),
    Wait(0.5),
    FadeOut(circle)
))
```

### AnimationGroup

```python
# Fine control over grouping
self.play(AnimationGroup(
    Create(circle),
    Create(square),
    lag_ratio=0.5,
    run_time=2
))
```

## Best Practices

### Timing Guidelines

| Animation Type | run_time | wait_after |
|---------------|----------|------------|
| Title/label | 1.0s | 0.5s |
| Simple shape | 1.0s | 0.5s |
| Formula | 1.5-2.0s | 1.0-2.0s |
| Complex formula | 2.0s | 4.0s |
| Transform | 1.5-2.0s | 1.0s |
| Emphasis | 0.5-1.0s | 0.5s |
| Data reveal | 2.0-3.0s | 1.0s |

### Pacing Principles

```python
# Fast for familiar content
self.play(Write(text), run_time=0.5)

# Slow for new concepts
self.play(Write(new_formula), run_time=2)
self.wait(4)  # Time to comprehend

# Dramatic for key moments
self.play(Transform(a, b), run_time=3, rate_func=smooth)
self.play(Flash(result))
self.wait(6)  # Let it sink in
```

### Avoid Anti-Patterns

```python
# BAD: Too fast
self.play(Write(complex_formula), run_time=0.3)

# GOOD: Appropriate timing
self.play(Write(complex_formula), run_time=2)
self.wait(4)

# BAD: No breathing room
self.play(anim1)
self.play(anim2)
self.play(anim3)

# GOOD: Strategic pauses
self.play(anim1)
self.wait(1)
self.play(anim2)
self.wait(0.5)
self.play(anim3)
```
