# MathVizAgent

**A Claude Code plugin for creating 3Blue1Brown-style mathematical animations using Manim**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/choxos/MathVizAgent)
[![Manim](https://img.shields.io/badge/Manim-v0.19.x-green.svg)](https://www.manim.community/)

---

## Overview

MathVizAgent is a Claude Code plugin that helps you create production-quality mathematical animations using [Manim Community Edition](https://www.manim.community/). The plugin provides specialized agents, workflow commands, and methodology skills that encode the best practices from 3Blue1Brown-style educational videos.

### Key Features

- **6 Specialized Agents** - Expert agents for scene design, animation code, math rendering, camera work, and code review
- **4 Workflow Commands** - Streamlined commands for creating scenes, videos, and reviewing code
- **6 Methodology Skills** - Comprehensive knowledge bases covering Manim fundamentals to production standards
- **100+ Code Examples** - Production-ready patterns extracted from real-world animations

---

## Quick Start

### Installation

1. Clone this repository:
```bash
git clone https://github.com/choxos/MathVizAgent.git
cd MathVizAgent
```

2. Load the plugin in Claude Code:
```bash
claude --plugin .
```

### Basic Usage

Create your first animation:
```
> /create-scene

Topic: Pythagorean theorem visualization
Duration: 30 seconds
Style: 3Blue1Brown educational
```

---

## Agents

MathVizAgent uses a multi-agent architecture with specialized experts:

| Agent | Model | Purpose |
|-------|-------|---------|
| **viz-architect** | Haiku | Routes requests to specialist agents based on task type |
| **scene-designer** | Sonnet | Plans scene structure, timing, and visual hierarchy |
| **animation-specialist** | Sonnet | Creates production-quality Manim animation code |
| **math-renderer** | Sonnet | Handles mathematical typography with MathTex/Tex |
| **camera-specialist** | Sonnet | Manages camera movements, zoom, and pan effects |
| **code-reviewer** | Sonnet | Reviews code for production quality standards |

### Agent Workflow

```
User Request
    │
    ▼
viz-architect (Router)
    │
    ├──► scene-designer ──► animation-specialist
    │                              │
    │                    ┌─────────┴─────────┐
    │                    ▼                   ▼
    │              math-renderer    camera-specialist
    │                    │                   │
    │                    └─────────┬─────────┘
    │                              ▼
    └──────────────────► code-reviewer
                               │
                               ▼
                        Final Output
```

---

## Commands

### `/create-scene`
Create a single animated scene with full agent coordination:
1. Gather requirements (topic, duration, complexity)
2. Design scene structure with timing
3. Generate animation code
4. Add mathematical formulas (if needed)
5. Add camera work (if needed)
6. Review for quality
7. Output complete scene

### `/create-video`
Create multi-scene educational videos:
- Supports short (3-5 min), medium (5-10 min), and long (10-15 min) formats
- Generates shared configuration for consistent styling
- Creates filelist.txt for FFmpeg concatenation
- Provides rendering commands

### `/review-animation`
Review existing Manim code:
- Automated quality checks
- A/B/C/D quality grades
- Specific improvement suggestions
- Optional refactoring

### `/render-preview`
Get rendering commands for all quality levels:
```bash
# Quick preview (480p)
manim -pql scene.py SceneName

# Medium quality (720p)
manim -pqm scene.py SceneName

# High quality (1080p)
manim -pqh scene.py SceneName
```

---

## Skills

Methodology knowledge bases that inform all agents:

| Skill | Description |
|-------|-------------|
| **manim-fundamentals** | Scene lifecycle, Mobject hierarchy, coordinate systems, rate functions |
| **animation-patterns** | Reveal, transform, progressive reveal, emphasis, and cleanup patterns |
| **camera-techniques** | Zoom, pan, save/restore state, line width compensation |
| **math-typography** | MathTex vs Tex, tex_to_color_map, custom equation classes |
| **component-design** | VGroup subclasses, helper methods, always_redraw patterns |
| **production-quality** | 3Blue1Brown-style standards for colors, timing, positioning |

---

## Production Quality Standards

MathVizAgent enforces 3Blue1Brown-style production standards:

### Semantic Color Palette (5 colors max)

```python
COLORS = {
    "PRIMARY": BLUE,        # Main concept
    "SECONDARY": ORANGE,    # Supporting concept
    "POSITIVE": GREEN,      # Success, correct
    "EMPHASIS": PURPLE,     # Highlights
    "MUTED": GRAY,          # De-emphasized
}
```

### Timing Guidelines

| Content Type | Animation | Wait After |
|--------------|-----------|------------|
| Simple shape | 0.5-1.0s | 0.5s |
| Text/label | 1.0s | 0.5s |
| Formula | 1.5-2.0s | 1.0-2.0s |
| Complex formula | 2.0-3.0s | 4.0s |
| Key insight | 3.0s | 6.0s |

### Core Principles

- **One concept per scene** - Keep scenes focused
- **No magic numbers** - Use relative positioning
- **Explicit object tracking** - Track objects for clean transitions
- **Strategic pauses** - Give viewers time to process

---

## Example Usage

### Creating a Simple Scene

```python
from manim import *

COLORS = {
    "PRIMARY": BLUE,
    "SECONDARY": ORANGE,
    "EMPHASIS": PURPLE,
}

TIMING = {
    "NORMAL": 1.0,
    "SLOW": 2.0,
    "COMPLEX": 4.0,
}

class PythagoreanTheorem(Scene):
    def construct(self):
        self.next_section("Title")

        title = Text("Pythagorean Theorem", font_size=52)
        self.play(Write(title), run_time=TIMING["NORMAL"])
        self.wait(TIMING["NORMAL"])
        self.play(title.animate.to_edge(UP, buff=0.5))

        self.next_section("Triangle")

        triangle = Polygon(
            ORIGIN,
            RIGHT * 3,
            RIGHT * 3 + UP * 4,
            color=COLORS["PRIMARY"]
        ).move_to(ORIGIN)

        self.play(Create(triangle), run_time=TIMING["SLOW"])
        self.wait(TIMING["NORMAL"])

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
        self.wait(TIMING["COMPLEX"])
```

---

## Requirements

- **Claude Code CLI** - [Installation guide](https://github.com/anthropics/claude-code)
- **Manim Community Edition** v0.19.x
- **Python** 3.8+
- **LaTeX** (for mathematical typography)

### Installing Manim

```bash
# macOS
brew install py3cairo ffmpeg
pip install manim

# Ubuntu/Debian
sudo apt install libcairo2-dev ffmpeg
pip install manim

# Windows
# See https://docs.manim.community/en/stable/installation.html
```

---

## Project Structure

```
MathVizAgent/
├── .claude-plugin/
│   └── marketplace.json           # Plugin configuration
├── plugins/
│   └── manim-viz/
│       ├── agents/
│       │   ├── viz-architect.md       # Router agent
│       │   ├── scene-designer.md      # Scene planning
│       │   ├── animation-specialist.md # Animation code
│       │   ├── math-renderer.md       # Math/LaTeX
│       │   ├── camera-specialist.md   # Camera work
│       │   └── code-reviewer.md       # Quality review
│       ├── commands/
│       │   ├── create-scene.md        # Single scene workflow
│       │   ├── create-video.md        # Multi-scene workflow
│       │   ├── review-animation.md    # Code review
│       │   └── render-preview.md      # Rendering commands
│       └── skills/
│           ├── manim-fundamentals/    # Core concepts
│           ├── animation-patterns/    # Animation patterns
│           ├── camera-techniques/     # Camera work
│           ├── math-typography/       # Math rendering
│           ├── component-design/      # VGroup patterns
│           └── production-quality/    # 3b1b standards
└── ref/
    ├── manim-docs/                    # Manim documentation
    └── 3mds/                          # Reference examples
```

---

## Reference Materials

The plugin includes reference materials for learning:

### 3mds Examples
Production-quality examples demonstrating:
- Custom VGroup subclasses (PDFPlot, CDFPlot, NNNode)
- ValueTracker + always_redraw() patterns
- MovingCameraScene with zoom/pan
- Line width compensation during camera zoom
- LaggedStart/LaggedStartMap animations

### Manim Documentation
- 385 API reference pages
- Tutorials and guides
- Configuration documentation

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Credits

**Author:** Ahmad Sofi-Mahmudi ([@choxos](https://github.com/choxos))

**Inspired by:**
- [3Blue1Brown](https://www.3blue1brown.com/) - Grant Sanderson's mathematical animations
- [Manim Community](https://www.manim.community/) - The animation engine
- [BiostatAgent](https://github.com/choxos/BiostatAgent) - Plugin architecture reference

---

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request
