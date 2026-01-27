---
name: production-quality
description: 3Blue1Brown-style production standards including color palettes, timing guidelines, positioning rules, cleanup patterns, and typography standards.
---

# Production Quality Standards

3Blue1Brown-style standards for professional mathematical animations.

## Color Standards

### Semantic Color Palette

Limit to 5 semantic colors per video:

```python
COLORS = {
    "PRIMARY": BLUE,        # Main concept
    "SECONDARY": ORANGE,    # Supporting/contrasting concept
    "POSITIVE": GREEN,      # Success, correct, good
    "EMPHASIS": PURPLE,     # Highlights, key insights
    "MUTED": GRAY,          # De-emphasized, background
}
```

### Color Guidelines

| Use Case | Color | Example |
|----------|-------|---------|
| Main variable/concept | PRIMARY | x, f(x) |
| Secondary variable | SECONDARY | y, derivative |
| Correct answer | POSITIVE | Checkmarks, solutions |
| Key insight | EMPHASIS | Critical formulas |
| Background/old info | MUTED | Previous steps |

### Avoid

- More than 5 distinct colors
- RED for general highlighting (reserve for errors/warnings)
- Similar shades that are hard to distinguish
- Colors that don't contrast with black background

## Timing Standards

### Animation Durations

| Content Type | run_time | Notes |
|--------------|----------|-------|
| Simple shape | 0.5-1.0s | Basic geometric objects |
| Text/label | 1.0s | Short text, labels |
| Formula | 1.5-2.0s | Mathematical expressions |
| Complex formula | 2.0-3.0s | Multi-part equations |
| Transform | 1.5-2.0s | Shape morphing |
| Dramatic reveal | 3.0s | Key moments |

### Wait Times

| Context | wait() | Purpose |
|---------|--------|---------|
| After label | 0.5s | Quick read |
| After simple content | 1.0s | Standard pause |
| After new concept | 2.0s | Processing time |
| After complex content | 4.0s | Deep comprehension |
| After key insight | 6.0s | Let it sink in |

### Timing Constants

```python
TIMING = {
    # Animation durations
    "FAST": 0.5,
    "NORMAL": 1.0,
    "SLOW": 2.0,
    "DRAMATIC": 3.0,

    # Wait times
    "BRIEF": 0.5,
    "STANDARD": 1.0,
    "EXTENDED": 2.0,
    "COMPLEX": 4.0,
    "CRITICAL": 6.0,
}
```

### Anti-Patterns

```python
# TOO FAST - viewer can't process
self.play(Write(formula), run_time=0.3)

# NO BREATHING ROOM
self.play(anim1)
self.play(anim2)
self.play(anim3)

# GOOD - appropriate pacing
self.play(Write(formula), run_time=2)
self.wait(4)  # Time to understand
self.play(next_animation)
```

## Positioning Standards

### Never Use Magic Numbers

```python
# BAD - Magic numbers
element.shift(LEFT * 3.5)
title.move_to([2, 3, 0])
box.scale(0.73)

# GOOD - Relative positioning
element.move_to(LEFT * config.frame_width / 4)
title.to_edge(UP, buff=0.5)
box.scale_to_fit_width(axes.width * 0.3)
```

### Positioning Methods

```python
# Relative to screen
obj.to_edge(UP, buff=0.5)
obj.to_corner(UR)
obj.move_to(ORIGIN)

# Relative to frame
obj.move_to(LEFT * config.frame_width / 4)
obj.move_to(UP * config.frame_height / 3)

# Relative to objects
obj.next_to(other, RIGHT, buff=1.0)
obj.align_to(other, UP)

# Group arrangement
VGroup(a, b, c).arrange(DOWN, buff=0.5)
VGroup(a, b, c).arrange_in_grid(rows=2)
```

### Standard Buffers

| Context | buff | Use |
|---------|------|-----|
| Labels | 0.1-0.2 | Next to objects |
| Grouped elements | 0.5 | Within groups |
| Major sections | 1.0 | Between groups |
| Screen edges | 0.5 | Edge padding |

## Scene Organization

### One Concept Per Scene

Each scene should convey ONE main idea:

```python
# BAD - Multiple concepts
class OverloadedScene(Scene):
    def construct(self):
        # Concept 1: Definition
        # Concept 2: Properties
        # Concept 3: Examples
        # Concept 4: Proofs

# GOOD - Focused scenes
class DefinitionScene(Scene): ...
class PropertiesScene(Scene): ...
class ExampleScene(Scene): ...
class ProofScene(Scene): ...
```

### Section Organization

```python
class WellOrganizedScene(Scene):
    def construct(self):
        self.next_section("Introduction")
        self.show_title()

        self.next_section("Main Concept")
        self.show_visualization()

        self.next_section("Key Insight")
        self.show_insight()

        self.next_section("Conclusion")
        self.show_summary()

    def show_title(self):
        """Isolated logic for title"""
        pass

    def show_visualization(self):
        """Isolated logic for main content"""
        pass
```

## Object Tracking

### Track Everything

```python
class ProductionScene(Scene):
    def setup(self):
        self.tracked = []

    def track(self, *mobjects):
        """Add mobjects to tracking and scene"""
        for m in mobjects:
            self.tracked.append(m)
            self.add(m)
        return mobjects[0] if len(mobjects) == 1 else mobjects

    def cleanup(self, keep=None):
        """Fade out tracked objects except those in keep"""
        keep = keep or []
        to_remove = [m for m in self.tracked if m not in keep]
        if to_remove:
            self.play(*[FadeOut(m) for m in to_remove])
        self.tracked = list(keep)
```

### Avoid Nuclear Cleanup

```python
# BAD - Destroys everything blindly
self.play(FadeOut(*self.mobjects))

# GOOD - Explicit control
self.cleanup(keep=[title, axes])
```

## Typography Standards

### Font Size Scale

```python
FONTS = {
    "TITLE": 52,      # Scene titles
    "SECTION": 42,    # Section headers
    "BODY": 28,       # Body text
    "SMALL": 24,      # Annotations
    "FORMULA": 38,    # Standalone formulas
}
```

### Text Guidelines

- Use consistent font sizes
- Limit text on screen (prefer visuals)
- Use Text() for prose, MathTex() for math
- Avoid orphaned words (single word on line)

## Visual Hierarchy

### Primary → Secondary → Tertiary

```python
# Primary focus: Large, bright, centered
main_concept = ...
main_concept.scale(1.2).set_color(COLORS["PRIMARY"])

# Secondary: Normal size, slightly dimmer
support = ...
support.set_color(COLORS["SECONDARY"])

# Tertiary: Smaller, muted
background = ...
background.scale(0.8).set_color(COLORS["MUTED"])
```

### Attention Management

```python
# Dim old content when showing new
old_content.animate.set_opacity(0.3)

# Highlight current focus
current.animate.set_color(COLORS["EMPHASIS"])

# Use movement to guide eye
new_element.animate.shift(target_position)
```

## Quality Checklist

Before rendering, verify:

### Colors
- [ ] Maximum 5 semantic colors
- [ ] Each color has consistent meaning
- [ ] All colors visible on black background

### Timing
- [ ] No animations under 0.5s
- [ ] Wait after complex content
- [ ] Pacing matches content complexity

### Positioning
- [ ] No magic numbers
- [ ] All positions relative
- [ ] Consistent spacing/buffers

### Organization
- [ ] One concept per scene
- [ ] Sections marked with next_section()
- [ ] Objects tracked for cleanup

### Typography
- [ ] Consistent font sizes
- [ ] Minimal on-screen text
- [ ] Formulas properly formatted

## Production Workflow

### 1. Planning
- Define ONE concept for scene
- Plan timeline with durations
- Identify color assignments

### 2. Implementation
- Use config constants
- Track all objects
- Use helper methods

### 3. Review
- Run quality checklist
- Preview at low quality
- Check timing feels natural

### 4. Polish
- Fine-tune timing
- Adjust colors for clarity
- Ensure smooth transitions

### 5. Export
- Render at target quality
- Verify output plays correctly
- Check file size is reasonable

## Example: Production-Quality Scene

```python
from manim import *

COLORS = {
    "PRIMARY": BLUE,
    "SECONDARY": ORANGE,
    "POSITIVE": GREEN,
    "EMPHASIS": PURPLE,
    "MUTED": GRAY,
}

TIMING = {
    "NORMAL": 1.0,
    "SLOW": 2.0,
    "COMPLEX": 4.0,
}

FONTS = {
    "TITLE": 52,
    "BODY": 28,
}

class ProductionExample(Scene):
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

    def construct(self):
        # Section 1: Title
        self.next_section("Title")

        title = Text("Pythagorean Theorem", font_size=FONTS["TITLE"])
        self.play(GrowFromCenter(title), run_time=TIMING["NORMAL"])
        self.wait(TIMING["NORMAL"])
        self.play(title.animate.to_edge(UP, buff=0.5))

        # Section 2: Visualization
        self.next_section("Visualization")

        # Create triangle (no magic numbers)
        triangle = Polygon(
            ORIGIN,
            RIGHT * 3,
            RIGHT * 3 + UP * 4,
            color=COLORS["PRIMARY"]
        ).move_to(ORIGIN)

        self.play(Create(triangle), run_time=TIMING["SLOW"])
        self.wait(TIMING["NORMAL"])

        # Section 3: Formula
        self.next_section("Formula")

        formula = MathTex(
            r"a^2 + b^2 = c^2",
            tex_to_color_map={
                r"a": COLORS["PRIMARY"],
                r"b": COLORS["SECONDARY"],
                r"c": COLORS["EMPHASIS"],
            }
        )
        formula.next_to(triangle, RIGHT, buff=1.0)

        self.play(Write(formula), run_time=TIMING["SLOW"])
        self.wait(TIMING["COMPLEX"])  # Key concept needs time

        # Section 4: Emphasis
        self.next_section("Emphasis")

        self.play(Circumscribe(formula, color=COLORS["EMPHASIS"]))
        self.wait(TIMING["NORMAL"])
```
