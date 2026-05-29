---
name: manim-fundamentals
description: Core Manim concepts including Scene lifecycle, Mobject hierarchy, coordinate systems, animation lifecycle, and rate functions.
---

# Manim Fundamentals

Core concepts for Manim Community Edition v0.20.1.

## Scene Lifecycle

### Scene Structure

```python
class MyScene(Scene):
    def setup(self):
        """Called once before construct()
        - Initialize instance variables
        - Create shared objects
        - Set up tracking systems
        """
        self.tracked_objects = []

    def construct(self):
        """Main scene logic
        - Create mobjects
        - Define animations
        - Control timing
        """
        circle = Circle()
        self.play(Create(circle))

    def tear_down(self):
        """Called after construct()
        - Cleanup (rarely needed)
        """
        pass
```

### Scene Types

| Scene Type | Use Case | Camera |
|------------|----------|--------|
| `Scene` | Standard 2D animations | Static |
| `MovingCameraScene` | 2D with zoom/pan | Movable 2D |
| `ThreeDScene` | 3D animations | 3D rotation |
| `ZoomedScene` | Picture-in-picture zoom | Zoom inset |

## Mobject Hierarchy

### Base Classes

```
Mobject (base)
├── VMobject (vector)
│   ├── VGroup
│   ├── Text, Tex, MathTex
│   ├── Circle, Square, Rectangle
│   ├── Line, Arrow, DashedLine
│   ├── Axes, NumberPlane
│   └── Graph, BarChart
├── ImageMobject
├── Group
└── ValueTracker
```

### VGroup - Container for VMobjects

```python
# Create VGroup
group = VGroup(circle, square, triangle)

# Access elements
group[0]  # first element
group[-1]  # last element

# Add/remove
group.add(new_element)
group.remove(element)

# Arrange
group.arrange(DOWN, buff=0.5)
group.arrange_in_grid(rows=2, cols=3)
```

### Common Mobject Methods

```python
# Positioning
obj.move_to(point)           # Absolute position
obj.next_to(other, RIGHT)    # Relative to object
obj.to_edge(UP, buff=0.5)    # Screen edge
obj.to_corner(UL)            # Screen corner
obj.shift(UP * 2)            # Relative shift
obj.align_to(other, LEFT)    # Align edges

# Transformations
obj.scale(0.5)               # Scale uniformly
obj.stretch(2, dim=0)        # Stretch in x
obj.rotate(PI/4)             # Rotate radians
obj.flip()                   # Mirror

# Appearance
obj.set_color(RED)
obj.set_fill(BLUE, opacity=0.5)
obj.set_stroke(WHITE, width=2)

# Information
obj.get_center()
obj.get_width()
obj.get_height()
obj.get_corner(UR)
```

## Coordinate Systems

### Default Frame

```
Frame dimensions: 14.22 x 8 units
Origin: center of screen
+X: right, +Y: up, +Z: out of screen

config.frame_width   # ~14.22
config.frame_height  # 8
```

### Direction Constants

```python
UP    = [0, 1, 0]
DOWN  = [0, -1, 0]
LEFT  = [-1, 0, 0]
RIGHT = [1, 0, 0]
UL = UP + LEFT      # Upper left
UR = UP + RIGHT     # Upper right
DL = DOWN + LEFT    # Down left
DR = DOWN + RIGHT   # Down right
ORIGIN = [0, 0, 0]
```

### Axes Coordinate System

```python
axes = Axes(
    x_range=[-3, 3, 1],   # [min, max, step]
    y_range=[-2, 2, 0.5],
    x_length=8,           # Screen units
    y_length=5,
)

# Convert between coordinate systems
point = axes.c2p(x, y)        # Axes coords → screen point
x, y = axes.p2c(point)        # Screen point → axes coords

# Plot function
graph = axes.plot(lambda x: x**2, color=BLUE)

# Get point on graph
point = graph.get_point_from_function(x_value)
```

## Animation Lifecycle

### Basic Animation

```python
self.play(animation, run_time=1)  # Default 1 second
self.wait(duration)                # Pause
```

### Animation Parameters

```python
self.play(
    Create(circle),
    run_time=2,              # Duration in seconds
    rate_func=smooth,        # Timing curve
    lag_ratio=0.5,           # For composed animations
)
```

### Animation Sequencing

```python
# Simultaneous
self.play(anim1, anim2)

# Sequential in one play
self.play(Succession(anim1, anim2, anim3))

# Staggered start
self.play(LaggedStart(anim1, anim2, anim3, lag_ratio=0.3))

# With waits between
self.play(anim1)
self.wait(0.5)
self.play(anim2)
```

## Rate Functions

Rate functions control animation timing curves.

### Built-in Rate Functions

```python
# Linear (constant speed)
rate_func=linear

# Smooth (ease in/out)
rate_func=smooth           # Default

# Rush functions
rate_func=rush_into        # Accelerate at end
rate_func=rush_from        # Decelerate from start

# There and back
rate_func=there_and_back   # Go and return
rate_func=there_and_back_with_pause

# Exponential
rate_func=exponential_decay

# Step functions
rate_func=lingering        # Slow at end
rate_func=running_start    # Overshoot at start

# Double smooth
rate_func=double_smooth    # Extra smooth
```

### Custom Rate Function

```python
def my_rate_func(t):
    """
    t: progress from 0 to 1
    return: modified progress 0 to 1
    """
    return t ** 2  # Quadratic ease-in

self.play(Create(circle), rate_func=my_rate_func)
```

## Configuration

### Config Object

```python
from manim import config

# Frame settings
config.frame_width   # Width in units
config.frame_height  # Height in units
config.pixel_width   # Output width in pixels
config.pixel_height  # Output height in pixels

# Quality presets
config.quality      # "low_quality", "medium_quality", etc.
config.frame_rate   # FPS

# Colors
config.background_color  # Scene background
```

### Quality Settings

| Quality | Resolution | FPS |
|---------|------------|-----|
| low | 480p | 15 |
| medium | 720p | 30 |
| high | 1080p | 60 |
| fourk | 2160p | 60 |

## Key Imports

```python
from manim import *  # Import everything

# Or selective imports
from manim import (
    Scene,
    Circle, Square, Rectangle,
    Text, Tex, MathTex,
    VGroup,
    Create, Write, FadeIn, FadeOut,
    Transform, ReplacementTransform,
    Axes, NumberPlane,
    ValueTracker,
    always_redraw,
    config,
    UP, DOWN, LEFT, RIGHT, ORIGIN,
    BLUE, RED, GREEN, YELLOW, WHITE,
)
```

## Common Patterns

### Add Then Animate

```python
# Create and add
circle = Circle()
self.add(circle)

# Then animate changes
self.play(circle.animate.shift(RIGHT * 2))
self.play(circle.animate.scale(0.5).set_color(RED))
```

### Animate Method

```python
# .animate creates animation from property changes
self.play(
    obj.animate.shift(UP * 2),        # Move
    obj.animate.scale(1.5),           # Scale
    obj.animate.set_color(RED),       # Color
    obj.animate.rotate(PI/4),         # Rotate
)

# Chain multiple changes
self.play(
    obj.animate.shift(UP).scale(0.5).set_color(BLUE)
)
```

### Copy Pattern

```python
# Create a copy to transform
copy = original.copy()
self.play(Transform(original, target))

# Or for cleaner replacement
self.play(ReplacementTransform(original, target))
```
