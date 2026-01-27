---
name: create-video
description: Create a multi-scene educational video with proper structure and concatenation
---

# /create-video - Multi-Scene Video Creation Workflow

Create a complete educational video with multiple scenes, consistent styling, and proper concatenation.

## Workflow Steps

### Step 1: Video Planning (@viz-architect)

Gather high-level requirements:

1. **Topic**: Main subject of the video
2. **Target Audience**: Beginner / Intermediate / Advanced
3. **Duration**: Short (3-5 min) / Medium (5-10 min) / Long (10-15 min)
4. **Style Preferences**: Colors, pacing, formality

### Step 2: Scene Breakdown (@scene-designer)

Create the video structure:

```markdown
# Video: [Title]

**Total Duration**: ~X minutes
**Scene Count**: Y scenes

## Scene List

| # | Scene Name | Duration | Concept | Camera |
|---|------------|----------|---------|--------|
| 1 | TitleScene | 20s | Video title | static |
| 2 | MotivationScene | 60s | Why this matters | static |
| 3 | ... | ... | ... | ... |

## Detailed Scene Plans

### Scene 1: TitleScene (20s)
[Timeline and technical requirements]

### Scene 2: MotivationScene (60s)
[Timeline and technical requirements]

...
```

### Step 3: Shared Configuration

Create a config module used by all scenes:

```python
# video_config.py
from manim import *

# Video Title
VIDEO_TITLE = "Understanding [Topic]"

# Semantic Color Palette (consistent across all scenes)
COLORS = {
    "PRIMARY": BLUE,        # Main concept (e.g., the function)
    "SECONDARY": ORANGE,    # Supporting concept (e.g., derivatives)
    "POSITIVE": GREEN,      # Success, correct answers
    "EMPHASIS": PURPLE,     # Highlights, key insights
    "MUTED": GRAY,          # De-emphasized, background
}

# Typography
FONTS = {
    "TITLE": 52,
    "SECTION": 42,
    "BODY": 28,
    "SMALL": 24,
    "FORMULA": 38,
}

# Timing Constants
TIMING = {
    "FAST": 0.5,
    "NORMAL": 1.0,
    "SLOW": 2.0,
    "DRAMATIC": 3.0,
    "BRIEF": 0.5,
    "STANDARD": 1.0,
    "EXTENDED": 2.0,
    "COMPLEX": 4.0,
    "CRITICAL": 6.0,
}

# Base Scene Class
class VideoScene(Scene):
    """Base class for all scenes in this video"""

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

    def section_title(self, text):
        """Standard section title animation"""
        title = Text(text, font_size=FONTS["SECTION"])
        self.play(Write(title), run_time=TIMING["NORMAL"])
        self.wait(TIMING["BRIEF"])
        self.play(title.animate.to_edge(UP, buff=0.5))
        return title
```

### Step 4: Scene Generation

For each scene in the breakdown:

1. Route to @animation-specialist for code
2. Include @math-renderer if formulas needed
3. Include @camera-specialist if camera work needed

Generate scenes in a single file:

```python
# video_scenes.py
from manim import *
from video_config import *

class Scene01_Title(VideoScene):
    def construct(self):
        # Title scene implementation
        ...

class Scene02_Motivation(VideoScene):
    def construct(self):
        # Motivation scene implementation
        ...

class Scene03_Concept(VideoScene):
    def construct(self):
        # Main concept implementation
        ...

# ... more scenes
```

### Step 5: Quality Review (@code-reviewer)

Review all scenes for:

- [ ] Consistent color usage across scenes
- [ ] Consistent timing patterns
- [ ] Smooth transitions between scenes
- [ ] No magic numbers
- [ ] Proper object cleanup
- [ ] One concept per scene

### Step 6: Render All Scenes

```bash
# Render all scenes to individual files
manim -qm video_scenes.py -a

# This creates files in media/videos/video_scenes/720p30/
```

### Step 7: Create Concatenation File

Generate `filelist.txt`:

```
file 'Scene01_Title.mp4'
file 'Scene02_Motivation.mp4'
file 'Scene03_Concept.mp4'
...
```

### Step 8: Concatenate Video

```bash
# Navigate to output directory
cd media/videos/video_scenes/720p30/

# Create filelist.txt with scene order
cat > filelist.txt << 'EOF'
file 'Scene01_Title.mp4'
file 'Scene02_Motivation.mp4'
file 'Scene03_Concept.mp4'
EOF

# Concatenate without re-encoding (fast)
ffmpeg -f concat -safe 0 -i filelist.txt -c copy ../../../final_video.mp4

# Or with re-encoding (for consistent quality)
ffmpeg -f concat -safe 0 -i filelist.txt -c:v libx264 -crf 18 ../../../final_video.mp4
```

## Video Structure Templates

### Short Video (3-5 minutes)
```
1. TitleScene (15-20s)
2. MotivationScene (45-60s)
3. ConceptScene (60-90s)
4. ExampleScene (60-90s)
5. SummaryScene (30-45s)
```

### Medium Video (5-10 minutes)
```
1. TitleScene (20s)
2. MotivationScene (60s)
3. BackgroundScene (60-90s)
4. ConceptScene1 (60-90s)
5. ConceptScene2 (60-90s)
6. ConnectionScene (45-60s)
7. ExampleScene (90s)
8. ApplicationScene (60s)
9. SummaryScene (45s)
10. CreditsScene (15s)
```

### Long Video (10-15 minutes)
Structure as 3-4 "chapters", each following short video pattern.

## Transition Best Practices

### Between Scenes
- End each scene with slight fade or pause
- Begin next scene with title or context
- Maintain visual continuity (consistent positioning)

### Visual Continuity
- Keep recurring elements in same positions
- Use consistent animation directions
- Maintain color associations throughout

## Output Deliverables

1. **video_config.py**: Shared configuration
2. **video_scenes.py**: All scene classes
3. **filelist.txt**: Scene order for concatenation
4. **Render script**: Commands to build final video
5. **README.md**: Documentation for the video project
