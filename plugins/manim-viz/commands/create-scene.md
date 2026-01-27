---
name: create-scene
description: Create a single animated scene for mathematical visualization
---

# /create-scene - Single Scene Creation Workflow

Create a production-quality animated scene for a single mathematical concept.

## Workflow Steps

### Step 1: Requirements Gathering (@viz-architect)

Gather the following information from the user:

1. **Topic**: What mathematical concept to visualize?
2. **Duration**: Target scene duration (30s / 60s / 90s)
3. **Complexity**: Simple / Moderate / Complex
4. **Features needed**:
   - Mathematical formulas? (y/n)
   - Camera movements? (y/n)
   - Data visualization? (y/n)
   - Custom components? (y/n)

### Step 2: Scene Design (@scene-designer)

Create a detailed scene plan:

```markdown
## Scene: [Concept Name]

**Duration**: X seconds
**Concept**: One-sentence description

### Timeline
| Time | Element | Animation | Wait |
|------|---------|-----------|------|
| 0-5s | Title | GrowFromCenter | 0.5s |
| ... | ... | ... | ... |

### Key Moments
- [timestamp] Critical insight
- [timestamp] Transition point

### Technical Requirements
- Camera: [static / zoom / pan]
- Colors: [list semantic colors]
- Formulas: [list any MathTex needed]
- Components: [list any custom VGroups]
```

### Step 3: Formula Preparation (@math-renderer)

If mathematical formulas are needed:

1. Write MathTex code for each formula
2. Identify parts for coloring/animation
3. Create custom MathTex subclasses if complex
4. Document index maps for animation

### Step 4: Camera Planning (@camera-specialist)

If camera work is needed:

1. Specify scene type (MovingCameraScene, ThreeDScene)
2. Plan camera moments with timestamps
3. Determine if line width management needed
4. Set timing for camera movements

### Step 5: Code Generation (@animation-specialist)

Generate the complete scene code:

```python
from manim import *

# Configuration
COLORS = {
    "PRIMARY": BLUE,
    "SECONDARY": ORANGE,
    "POSITIVE": GREEN,
    "EMPHASIS": PURPLE,
    "MUTED": GRAY,
}

TIMING = {
    "FAST": 0.5,
    "NORMAL": 1.0,
    "SLOW": 2.0,
    "COMPLEX": 4.0,
}

class [SceneName](Scene):  # or MovingCameraScene
    def construct(self):
        # Section 1: Introduction
        self.next_section("Introduction")
        # ... code

        # Section 2: Main Visualization
        self.next_section("Main Content")
        # ... code

        # Section 3: Conclusion
        self.next_section("Conclusion")
        # ... code
```

### Step 6: Quality Review (@code-reviewer)

Review the generated code for:

- [ ] No magic numbers
- [ ] Semantic color usage
- [ ] Appropriate timing with waits
- [ ] Explicit object tracking
- [ ] Scene organization with sections
- [ ] Camera usage (if applicable)

### Step 7: Render Instructions

Provide rendering commands:

```bash
# Quick preview (480p, fast)
manim -pql scene_file.py SceneName

# Medium quality (720p)
manim -pqm scene_file.py SceneName

# High quality (1080p)
manim -pqh scene_file.py SceneName

# Production quality (1080p, 60fps)
manim -pqh --fps 60 scene_file.py SceneName
```

## Example Usage

**User Request**: "Create a scene showing the Central Limit Theorem with samples from a uniform distribution converging to normal"

**Output**:
1. Scene plan with 60s timeline
2. MathTex for CLT formula
3. Camera plan for zoom to histogram detail
4. Complete Python code
5. Render commands

## Handoff Protocol

When routing between agents:

1. **viz-architect → scene-designer**: Topic, duration, complexity, features
2. **scene-designer → animation-specialist**: Complete timeline, technical requirements
3. **scene-designer → math-renderer**: List of formulas needed
4. **scene-designer → camera-specialist**: Camera moments and timing
5. **animation-specialist → code-reviewer**: Complete code for review
6. **code-reviewer → user**: Final code with any fixes applied
