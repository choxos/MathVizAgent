# MathVizAgent

> **Production-Quality Mathematical Animation Agent** — 6 specialized agents, 4 workflow commands, and 6 methodology skills for 3Blue1Brown-style Manim videos

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) plugin for creating professional mathematical animations using [Manim Community Edition](https://www.manim.community/):

| Plugin | Focus | Agents | Commands | Skills |
|--------|-------|--------|----------|--------|
| **mathviz** | 3Blue1Brown-style animations | 6 | 4 | 6 |

## Quick Start

### 1. Add the Marketplace

```bash
/plugin marketplace add choxos/MathVizAgent
```

### 2. Install Plugin

```bash
/plugin install mathviz
```

### 3. Install Dependencies

```bash
# macOS
brew install py3cairo ffmpeg
pip install manim

# Ubuntu/Debian
sudo apt install libcairo2-dev ffmpeg
pip install manim
```

---

## Plugin Overview

### MathViz (`mathviz`)

Create production-quality mathematical animations following 3Blue1Brown standards:

- **Scene Design** — Structure, timing, visual hierarchy
- **Animation Code** — ValueTracker, always_redraw, LaggedStart patterns
- **Math Typography** — MathTex, Tex, semantic coloring
- **Camera Work** — Zoom, pan, save/restore state
- **Quality Standards** — 5 colors max, strategic timing, relative positioning

**Commands:**
| Command | Description |
|---------|-------------|
| `/create-scene` | Create single animated scene with full agent coordination |
| `/create-video` | Create multi-scene educational video |
| `/review-animation` | Review code for production quality standards |
| `/render-preview` | Get rendering commands for all quality levels |

**Agents:** viz-architect, scene-designer, animation-specialist, math-renderer, camera-specialist, code-reviewer

---

## Repository Structure

```
MathVizAgent/
├── .claude-plugin/
│   └── marketplace.json              # Plugin manifest
├── plugins/
│   └── mathviz/
│       ├── agents/                   # 6 agents
│       ├── commands/                 # 4 commands
│       └── skills/                   # 6 skills
├── README.md
└── LICENSE
```

---

## Usage Examples

### Create a Scene

```
Create a scene visualizing the Pythagorean theorem.
Include the triangle and the formula a² + b² = c².
```

### Create a Video

```
Create a 5-minute educational video about derivatives.
Include definition, geometric interpretation, and examples.
```

### Review Animation

```
Review my Manim code for production quality.
Check for magic numbers, timing, and color usage.
```

---

## All Components

### Agents (6 total)

| Agent | Model | Purpose |
|-------|-------|---------|
| viz-architect | Haiku | Routes requests to specialist agents |
| scene-designer | Sonnet | Plans scene structure and timing |
| animation-specialist | Sonnet | Creates production animation code |
| math-renderer | Sonnet | Handles MathTex/Tex typography |
| camera-specialist | Sonnet | Manages zoom, pan, camera work |
| code-reviewer | Sonnet | Reviews for quality standards |

### Commands (4 total)

| Command | Description |
|---------|-------------|
| `/create-scene` | Single scene with agent coordination |
| `/create-video` | Multi-scene video with consistency |
| `/review-animation` | Quality check with A/B/C/D grades |
| `/render-preview` | Rendering commands for all quality levels |

### Skills (6 total)

| Skill | Coverage |
|-------|----------|
| manim-fundamentals | Scene lifecycle, Mobject hierarchy, coordinates, rate functions |
| animation-patterns | Reveal, transform, progressive reveal, emphasis, cleanup |
| camera-techniques | Zoom, pan, save/restore, line width compensation |
| math-typography | MathTex vs Tex, tex_to_color_map, custom equation classes |
| component-design | VGroup subclasses, helper methods, always_redraw |
| production-quality | 5 semantic colors, timing guidelines, positioning rules |

---

## Production Standards

MathVizAgent enforces 3Blue1Brown-style quality:

```python
# Semantic colors (5 max)
COLORS = {
    "PRIMARY": BLUE,      # Main concept
    "SECONDARY": ORANGE,  # Supporting
    "POSITIVE": GREEN,    # Success
    "EMPHASIS": PURPLE,   # Highlights
    "MUTED": GRAY,        # De-emphasized
}

# Strategic timing
TIMING = {
    "FAST": 0.5,      # Simple animations
    "NORMAL": 1.0,    # Standard
    "SLOW": 2.0,      # Complex content
    "COMPLEX": 4.0,   # Key insights (wait)
}
```

**Core Principles:**
- One concept per scene
- No magic numbers (relative positioning)
- Explicit object tracking
- Strategic pauses for comprehension

---

## License

MIT License — see [LICENSE](LICENSE) for details.

## Acknowledgments

- [3Blue1Brown](https://www.3blue1brown.com/) for animation inspiration
- [Manim Community](https://www.manim.community/) for the animation engine
- [Anthropic](https://www.anthropic.com/) for Claude Code
