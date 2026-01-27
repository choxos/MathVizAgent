---
name: camera-techniques
description: Camera manipulation including zoom, pan, save/restore state, line width compensation, and focus transitions.
---

# Camera Techniques

Cinematic camera techniques for Manim animations.

## MovingCameraScene (2D)

### Basic Setup

```python
from manim import *

class MyScene(MovingCameraScene):
    def construct(self):
        # self.camera.frame is the camera
        # It's a VMobject that can be animated

        # Save initial state
        self.camera.frame.save_state()

        # ... scene content
```

### Camera Properties

```python
# Get current properties
self.camera.frame.width      # Frame width
self.camera.frame.height     # Frame height
self.camera.frame.get_center()  # Camera position

# Set properties
self.camera.frame.set(width=10)
self.camera.frame.set(height=6)
```

## Zoom Operations

### Zoom In (Make Larger)

```python
# Scale down frame = zoom in
self.play(
    self.camera.frame.animate.scale(0.5),  # 2x zoom
    run_time=2
)

# Zoom to specific height
self.play(
    self.camera.frame.animate.set(height=4),
    run_time=2
)
```

### Zoom Out (Show More)

```python
# Scale up frame = zoom out
self.play(
    self.camera.frame.animate.scale(2),  # 0.5x zoom
    run_time=2
)
```

### Zoom to Object

```python
# Fit object in frame with padding
target = some_mobject

self.play(
    self.camera.frame.animate
        .set(height=target.height * 1.5)
        .move_to(target),
    run_time=2
)
```

### Zoom Levels

| Scale Factor | Effective Zoom | Use Case |
|--------------|----------------|----------|
| 0.25 | 4x zoom in | Fine detail |
| 0.5 | 2x zoom in | Detail view |
| 1.0 | Normal | Overview |
| 2.0 | 0.5x zoom out | Wide shot |

## Pan Operations

### Pan to Object

```python
self.play(
    self.camera.frame.animate.move_to(target),
    run_time=1.5
)
```

### Pan to Coordinates

```python
self.play(
    self.camera.frame.animate.move_to([3, 2, 0]),
    run_time=1.5
)
```

### Pan with Offset

```python
# Center slightly above target
self.play(
    self.camera.frame.animate.move_to(target).shift(UP * 0.5),
    run_time=1.5
)
```

## Combined Operations

### Zoom + Pan

```python
# Zoom into specific element
self.play(
    self.camera.frame.animate
        .scale(0.3)
        .move_to(detail_element),
    run_time=2
)
```

### Smooth Multi-Step

```python
# Step 1: Pan to area
self.play(
    self.camera.frame.animate.move_to(area),
    run_time=1
)

# Step 2: Zoom in
self.play(
    self.camera.frame.animate.scale(0.5),
    run_time=1.5
)
```

## Save/Restore Pattern

### Basic Usage

```python
class MyScene(MovingCameraScene):
    def construct(self):
        # Setup
        main_content = self.create_content()

        # Save the overview state
        self.camera.frame.save_state()

        # Show overview
        self.wait(2)

        # Zoom to detail
        self.play(
            self.camera.frame.animate
                .set(height=detail.height * 2)
                .move_to(detail),
            run_time=2
        )

        # Explain detail
        self.wait(3)

        # Return to overview
        self.play(Restore(self.camera.frame), run_time=2)
```

### Multiple Saves

```python
# Save state 1 (overview)
self.camera.frame.save_state()

self.play(self.camera.frame.animate.scale(0.5).move_to(area1))

# Save state 2 (area1)
state_area1 = self.camera.frame.copy()

self.play(Restore(self.camera.frame))  # Back to overview

# Later, return to area1
self.play(self.camera.frame.animate.become(state_area1))
```

## Line Width Management

### The Problem

When zooming, line widths don't scale, causing:
- Zoomed in: Lines appear thicker
- Zoomed out: Lines appear thinner

### The Solution

```python
class ConsistentLinesScene(MovingCameraScene):
    def construct(self):
        # Store initial values
        INITIAL_LINE_WIDTH = self.camera.cairo_line_width_multiple
        INITIAL_FRAME_WIDTH = config.frame_width

        # Create updater
        def maintain_line_width(m):
            proportion = self.camera.frame.width / INITIAL_FRAME_WIDTH
            self.camera.cairo_line_width_multiple = INITIAL_LINE_WIDTH * proportion

        # Attach to dummy mobject
        line_manager = Mobject()
        line_manager.add_updater(maintain_line_width)
        self.add(line_manager)

        # Now zoom operations maintain consistent lines
        # ... rest of scene
```

### When to Use

- Complex diagrams with many lines
- Significant zoom changes (>2x)
- When visual consistency matters

## Focus Transitions

### Sequential Focus Tour

```python
elements = [element_a, element_b, element_c]

self.camera.frame.save_state()

for element in elements:
    # Zoom to element
    self.play(
        self.camera.frame.animate
            .set(height=element.height * 2)
            .move_to(element),
        run_time=1.5
    )

    # Time to observe
    self.wait(2)

# Return to overview
self.play(Restore(self.camera.frame), run_time=1.5)
```

### Tour with Varying Zoom

```python
tour_stops = [
    (element_a, 1.5, 2.0),  # (element, height_factor, wait_time)
    (element_b, 2.0, 1.5),
    (element_c, 1.2, 3.0),
]

self.camera.frame.save_state()

for element, height_factor, wait in tour_stops:
    self.play(
        self.camera.frame.animate
            .set(height=element.height * height_factor)
            .move_to(element),
        run_time=1.5
    )
    self.wait(wait)

self.play(Restore(self.camera.frame), run_time=2)
```

## ThreeDScene Camera

### Setup

```python
class My3DScene(ThreeDScene):
    def construct(self):
        # Set initial orientation
        self.set_camera_orientation(
            phi=75 * DEGREES,    # Polar angle (from z-axis)
            theta=-45 * DEGREES  # Azimuthal angle (around z-axis)
        )
```

### Camera Rotation

```python
# Rotate to new orientation
self.move_camera(
    phi=60 * DEGREES,
    theta=-60 * DEGREES,
    run_time=2
)

# Continuous rotation
self.begin_ambient_camera_rotation(rate=0.1)  # rad/s
self.wait(5)
self.stop_ambient_camera_rotation()
```

### 3D Zoom

```python
# Adjust zoom
self.set_camera_orientation(zoom=0.5)  # Closer
self.set_camera_orientation(zoom=2)    # Further
```

## Timing Guidelines

| Movement | Duration | Notes |
|----------|----------|-------|
| Quick pan | 0.5-1.0s | Minor adjustment |
| Standard pan | 1.5s | Normal movement |
| Dramatic zoom in | 2.0-3.0s | Building focus |
| Slow reveal zoom out | 3.0-4.0s | Grand reveal |
| Restore overview | 2.0s | Return home |

## Rate Functions for Camera

```python
# Smooth (default) - professional
self.play(
    self.camera.frame.animate.move_to(target),
    rate_func=smooth
)

# Rush effects - dramatic
self.play(
    self.camera.frame.animate.scale(0.3),
    rate_func=rush_into  # Accelerate at end
)

self.play(
    Restore(self.camera.frame),
    rate_func=rush_from  # Decelerate from start
)

# Linear - mechanical feel
self.play(
    self.camera.frame.animate.shift(RIGHT * 3),
    rate_func=linear
)
```

## Complete Example

```python
from manim import *

class CameraDemo(MovingCameraScene):
    def construct(self):
        # Line width management
        INIT_WIDTH = self.camera.cairo_line_width_multiple
        INIT_FRAME = config.frame_width
        manager = Mobject()
        manager.add_updater(lambda m:
            setattr(self.camera, 'cairo_line_width_multiple',
                    INIT_WIDTH * self.camera.frame.width / INIT_FRAME))
        self.add(manager)

        # Create content
        circles = VGroup(*[
            Circle(radius=0.5).move_to([x, y, 0])
            for x in range(-4, 5, 2)
            for y in range(-2, 3, 2)
        ])
        self.add(circles)

        # Save overview
        self.camera.frame.save_state()
        self.wait(1)

        # Zoom to center circle
        center = circles[len(circles)//2]
        self.play(
            self.camera.frame.animate
                .set(height=center.height * 3)
                .move_to(center),
            run_time=2
        )

        # Add detail
        label = Text("Center", font_size=12).next_to(center, UP, buff=0.1)
        self.play(Write(label))
        self.wait(2)

        # Return to overview
        self.play(
            FadeOut(label),
            Restore(self.camera.frame),
            run_time=2
        )
        self.wait(1)
```
