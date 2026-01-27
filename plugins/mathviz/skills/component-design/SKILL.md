---
name: component-design
description: Reusable component patterns including VGroup subclasses, helper methods, encapsulated visualizations, and always_redraw patterns.
---

# Component Design

Patterns for creating reusable, well-structured Manim components.

## VGroup Subclass Pattern

### Basic Structure

```python
class MyComponent(VGroup):
    """Reusable visualization component"""

    def __init__(self, param1, param2, **kwargs):
        super().__init__(**kwargs)

        # Store parameters
        self.param1 = param1
        self.param2 = param2

        # Build component
        self._build()

    def _build(self):
        """Construct the component's mobjects"""
        # Create child mobjects
        self.part_a = Circle(radius=self.param1)
        self.part_b = Square(side_length=self.param2)

        # Position relative to each other
        self.part_b.next_to(self.part_a, RIGHT)

        # Add to self (VGroup)
        self.add(self.part_a, self.part_b)
```

### Usage

```python
# Create instance
component = MyComponent(1.0, 0.5)

# It's a VGroup, so all VGroup methods work
component.scale(0.5)
component.move_to(LEFT * 2)
component.set_color(BLUE)

# Animate
self.play(Create(component))
self.play(component.animate.shift(RIGHT * 3))
```

## Real-World Example: Distribution Plot

```python
from scipy.stats import norm

class DistributionPlot(VGroup):
    """Self-contained probability distribution visualization"""

    def __init__(
        self,
        func,                # Distribution function
        x_range=(-3, 3),     # Domain
        color=BLUE,
        show_axes=True,
        **kwargs
    ):
        super().__init__(**kwargs)

        self.func = func
        self.x_min, self.x_max = x_range
        self.color = color

        self._build(show_axes)

    def _build(self, show_axes):
        # Calculate y range from function
        x_vals = np.linspace(self.x_min, self.x_max, 100)
        y_vals = [self.func(x) for x in x_vals]
        y_max = max(y_vals) * 1.1

        # Create axes
        self.axes = Axes(
            x_range=[self.x_min, self.x_max, 1],
            y_range=[0, y_max, y_max/4],
            x_length=6,
            y_length=4,
            tips=False
        )

        # Create plot
        self.curve = self.axes.plot(self.func, color=self.color)

        # Add to component
        if show_axes:
            self.add(self.axes)
        self.add(self.curve)

    # Helper methods
    def get_point(self, x):
        """Get point on curve at x value"""
        return self.axes.c2p(x, self.func(x))

    def get_area(self, x_start, x_end, color=None):
        """Get shaded area under curve"""
        return self.axes.get_area(
            self.curve,
            x_range=[x_start, x_end],
            color=color or self.color
        )

    def get_vertical_line(self, x, color=RED):
        """Get vertical line from x-axis to curve"""
        return DashedLine(
            start=self.axes.c2p(x, 0),
            end=self.get_point(x),
            color=color
        )
```

### Using the Component

```python
class DemoScene(Scene):
    def construct(self):
        # Create distribution
        dist = DistributionPlot(
            func=lambda x: norm.pdf(x, 0, 1),
            x_range=(-3, 3),
            color=BLUE
        )

        self.play(Create(dist))

        # Use helper methods
        area = dist.get_area(-1, 1, color=YELLOW)
        self.play(FadeIn(area))

        line = dist.get_vertical_line(0)
        self.play(Create(line))
```

## Component with Dynamic Updates

### ValueTracker Integration

```python
class DynamicPlot(VGroup):
    """Plot with dynamic parameter"""

    def __init__(self, initial_value=1.0, **kwargs):
        super().__init__(**kwargs)

        # Create tracker
        self.value_tracker = ValueTracker(initial_value)

        # Build static parts
        self.axes = Axes(x_range=[-3, 3], y_range=[0, 1])
        self.add(self.axes)

        # Build dynamic parts
        self.curve = always_redraw(self._build_curve)
        self.add(self.curve)

    def _build_curve(self):
        """Rebuild curve based on current tracker value"""
        sigma = self.value_tracker.get_value()
        return self.axes.plot(
            lambda x: norm.pdf(x, 0, sigma),
            color=BLUE
        )

    def set_value(self, value, **kwargs):
        """Animate value change"""
        return self.value_tracker.animate.set_value(value, **kwargs)

    @property
    def value(self):
        return self.value_tracker.get_value()
```

### Usage with Animation

```python
class DynamicScene(Scene):
    def construct(self):
        plot = DynamicPlot(initial_value=1.0)
        self.add(plot)

        # Animate parameter change
        self.play(plot.set_value(0.5), run_time=2)
        self.play(plot.set_value(2.0), run_time=2)
```

## Encapsulated Coordinate System

```python
class PlotSystem(VGroup):
    """Coordinate system with encapsulated plotting"""

    def __init__(self, x_range, y_range, **kwargs):
        super().__init__(**kwargs)

        self.axes = Axes(
            x_range=x_range,
            y_range=y_range,
            tips=False
        )
        self.add(self.axes)

        self.plots = VGroup()
        self.add(self.plots)

    def c2p(self, x, y):
        """Convert coordinates to point"""
        return self.axes.c2p(x, y)

    def p2c(self, point):
        """Convert point to coordinates"""
        return self.axes.p2c(point)

    def plot(self, func, x_range=None, **kwargs):
        """Add a plot to the system"""
        plot = self.axes.plot(func, x_range=x_range, **kwargs)
        self.plots.add(plot)
        return plot

    def add_point(self, x, y, **kwargs):
        """Add a point to the system"""
        point = Dot(self.c2p(x, y), **kwargs)
        self.add(point)
        return point

    def add_data(self, data, **kwargs):
        """Add scatter points from data"""
        points = VGroup(*[
            Dot(self.c2p(x, y), **kwargs)
            for x, y in data
        ])
        self.add(points)
        return points
```

## Component Factory Pattern

```python
class ComponentFactory:
    """Factory for creating styled components"""

    # Shared configuration
    COLORS = {
        "primary": BLUE,
        "secondary": ORANGE,
        "accent": PURPLE
    }

    @classmethod
    def create_labeled_circle(cls, label_text, radius=0.5, color="primary"):
        """Create a circle with centered label"""
        group = VGroup()

        circle = Circle(radius=radius, color=cls.COLORS[color])
        label = Text(label_text, font_size=24)
        label.move_to(circle)

        group.add(circle, label)
        return group

    @classmethod
    def create_labeled_box(cls, title, content, width=3, color="primary"):
        """Create a box with title and content"""
        group = VGroup()

        box = Rectangle(width=width, height=2, color=cls.COLORS[color])
        title_text = Text(title, font_size=20, weight=BOLD)
        content_text = Text(content, font_size=16)

        title_text.next_to(box, UP, buff=0.1, aligned_edge=LEFT)
        content_text.move_to(box)

        group.add(box, title_text, content_text)
        return group
```

## Hierarchical Components

```python
class NeuralNetworkLayer(VGroup):
    """Single layer of neurons"""

    def __init__(self, n_neurons, radius=0.3, **kwargs):
        super().__init__(**kwargs)

        self.neurons = VGroup(*[
            Circle(radius=radius, color=WHITE)
            for _ in range(n_neurons)
        ]).arrange(DOWN, buff=0.5)

        self.add(self.neurons)

    def get_neuron(self, index):
        return self.neurons[index]


class NeuralNetwork(VGroup):
    """Complete neural network visualization"""

    def __init__(self, layer_sizes, **kwargs):
        super().__init__(**kwargs)

        # Create layers
        self.layers = VGroup(*[
            NeuralNetworkLayer(size)
            for size in layer_sizes
        ]).arrange(RIGHT, buff=2)

        # Create connections
        self.connections = VGroup()
        for i in range(len(self.layers) - 1):
            layer_connections = self._connect_layers(
                self.layers[i],
                self.layers[i + 1]
            )
            self.connections.add(layer_connections)

        self.add(self.connections, self.layers)

    def _connect_layers(self, layer1, layer2):
        """Create arrows between all neurons in adjacent layers"""
        arrows = VGroup()
        for n1 in layer1.neurons:
            for n2 in layer2.neurons:
                arrow = Arrow(
                    n1.get_right(),
                    n2.get_left(),
                    buff=0.05,
                    stroke_width=1
                )
                arrows.add(arrow)
        return arrows
```

## Best Practices

### 1. Single Responsibility

Each component should do one thing well:

```python
# Good: Focused component
class AxisLabels(VGroup):
    """Just handles axis labels"""
    pass

# Bad: Doing too much
class PlotWithLabelsAndLegendAndTitle(VGroup):
    pass
```

### 2. Configurable Defaults

```python
class FlexibleComponent(VGroup):
    DEFAULT_COLOR = BLUE
    DEFAULT_SIZE = 1.0

    def __init__(self, color=None, size=None, **kwargs):
        super().__init__(**kwargs)
        self.color = color or self.DEFAULT_COLOR
        self.size = size or self.DEFAULT_SIZE
```

### 3. Helper Methods

```python
class PlotComponent(VGroup):
    def get_point_at(self, x):
        """Get point on plot at x value"""
        pass

    def highlight_region(self, x1, x2):
        """Highlight a region of the plot"""
        pass

    def add_annotation(self, x, text):
        """Add text annotation at x"""
        pass
```

### 4. Clean Naming

```python
# Public attributes: no underscore
self.axes
self.curve

# Private/internal: leading underscore
self._temp_points
self._cached_values

# Methods: verbs for actions
def get_area(self, ...):
def create_label(self, ...):
def update_curve(self, ...):
```

### 5. Documentation

```python
class WellDocumentedComponent(VGroup):
    """Brief description of component.

    Longer description explaining use case and behavior.

    Args:
        param1: Description of param1
        param2: Description of param2

    Attributes:
        axes: The underlying Axes object
        curve: The plotted curve

    Example:
        >>> comp = WellDocumentedComponent(1.0, "label")
        >>> self.play(Create(comp))
    """
```
