---
name: review-animation
description: Review existing Manim code for production quality and suggest improvements
---

# /review-animation - Code Review Workflow

Review existing Manim animation code for production quality standards and provide actionable improvements.

## Workflow Steps

### Step 1: Code Submission

User provides their Manim code for review.

### Step 2: Automated Analysis (@code-reviewer)

Analyze the code against the following checklist:

#### Critical Issues (Must Fix)
- [ ] Magic numbers in positioning
- [ ] Missing waits after complex content
- [ ] Inconsistent or excessive colors (>5)
- [ ] No object tracking for cleanup
- [ ] Using `FadeOut(*self.mobjects)`
- [ ] Wrong scene type for camera work

#### Warnings (Should Fix)
- [ ] No `next_section()` in long scenes
- [ ] Multiple concepts in single scene
- [ ] Animations too fast (<0.5s run_time)
- [ ] No semantic color system
- [ ] Repeated code that should be components
- [ ] Hardcoded text sizes

#### Suggestions (Nice to Have)
- [ ] Could use LaggedStart instead of loop
- [ ] Custom component would improve readability
- [ ] Rate function could enhance feel
- [ ] Camera work could improve focus

### Step 3: Generate Review Report

```markdown
## Code Review: [Scene/File Name]

### Summary
| Metric | Value |
|--------|-------|
| Quality Score | [A/B/C/D] |
| Critical Issues | [count] |
| Warnings | [count] |
| Suggestions | [count] |
| Lines of Code | [count] |

### Critical Issues

#### 1. [Issue Title] (line X)

**Current Code**:
```python
element.shift(LEFT * 3.5)
```

**Problem**: Magic number makes code brittle and unclear.

**Fix**:
```python
element.move_to(LEFT * config.frame_width / 4)
```

---

### Warnings

#### 1. [Warning Title] (line Y)

**Current Code**:
```python
self.play(Write(complex_formula))
# Next animation immediately
```

**Problem**: No time for viewer to comprehend complex formula.

**Recommendation**: Add wait after complex content.
```python
self.play(Write(complex_formula), run_time=2)
self.wait(4)  # Time to comprehend
```

---

### Suggestions

#### 1. [Suggestion Title] (lines A-B)

**Current Pattern**:
```python
for item in items:
    self.play(Write(item), run_time=0.5)
```

**Improved Pattern**:
```python
self.play(LaggedStart(*[Write(item) for item in items], lag_ratio=0.1), run_time=3)
```

**Benefit**: Smoother animation, single render pass, professional feel.

---

### Refactored Code

If requested, provide complete refactored version:

```python
# Complete improved code here
```
```

### Step 4: Quality Score Criteria

| Score | Description | Criteria |
|-------|-------------|----------|
| **A** | Production Ready | 0 critical, ≤2 warnings |
| **B** | Minor Issues | 0 critical, 3-5 warnings |
| **C** | Needs Work | 1-2 critical OR >5 warnings |
| **D** | Major Revision | 3+ critical issues |

### Step 5: Optional Refactoring

If user requests, provide refactored code that:

1. Fixes all critical issues
2. Addresses warnings
3. Implements suggestions where beneficial
4. Maintains original functionality
5. Adds appropriate comments

## Review Categories

### 1. Positioning Review

Check for:
- Magic numbers (`LEFT * 3.5`, `[2, 3, 0]`)
- Absolute coordinates instead of relative
- Hardcoded shifts and scales

Recommend:
- `config.frame_width/height` for frame-relative
- `next_to()`, `align_to()` for object-relative
- `to_edge()`, `to_corner()` for screen positions
- `arrange()` for group layouts

### 2. Color Review

Check for:
- More than 5 distinct colors
- Inconsistent color usage (same concept, different colors)
- Hard-to-see colors on black background
- Hex codes instead of named constants

Recommend:
- Define semantic color palette
- Use color constants, not literals
- Test visibility on black background

### 3. Timing Review

Check for:
- Missing `wait()` calls
- Too fast animations (<0.5s)
- Inconsistent pacing
- No time for comprehension after reveals

Recommend:
- `wait()` after every significant reveal
- Minimum 1s for simple content, 4s for complex
- Define timing constants
- Match timing to content complexity

### 4. Structure Review

Check for:
- Long construct() without sections
- Multiple concepts in one scene
- No helper methods
- Repeated code patterns

Recommend:
- Use `next_section()` for organization
- One concept per scene
- Extract helper methods
- Create reusable components

### 5. Animation Review

Check for:
- Sequential individual animations (for loops)
- Wrong animation type for content
- Missing emphasis on key moments
- No rate functions for special effects

Recommend:
- `LaggedStart` for groups
- Match animation to content type
- `Indicate`, `Circumscribe`, `Flash` for emphasis
- Rate functions for drama

## Example Review Session

**User Input**:
```python
class MyScene(Scene):
    def construct(self):
        c = Circle()
        c.shift(LEFT * 3)
        self.play(Create(c))

        for i in range(10):
            d = Dot().move_to([i - 5, 0, 0])
            self.play(FadeIn(d), run_time=0.2)

        self.play(FadeOut(*self.mobjects))
```

**Review Output**:

```markdown
## Code Review: MyScene

### Summary
| Metric | Value |
|--------|-------|
| Quality Score | D |
| Critical Issues | 3 |
| Warnings | 2 |
| Suggestions | 1 |

### Critical Issues

#### 1. Magic Number (line 4)
- `LEFT * 3` - unclear why 3
- Fix: `c.to_edge(LEFT, buff=1)`

#### 2. Magic Coordinates (line 8)
- `[i - 5, 0, 0]` - hardcoded positions
- Fix: Use relative positioning or axes

#### 3. Nuclear Cleanup (line 10)
- `FadeOut(*self.mobjects)` loses control
- Fix: Track objects explicitly

### Warnings

#### 1. Animation Too Fast (line 9)
- 0.2s run_time barely visible
- Fix: Use LaggedStart with 2-3s total

#### 2. No Wait After Content
- No pause for viewing
- Fix: Add `self.wait(1)` after reveals

### Refactored Code

```python
class MyScene(Scene):
    def construct(self):
        # Circle on left side
        c = Circle()
        c.to_edge(LEFT, buff=1)
        self.play(Create(c))
        self.wait(0.5)

        # Create dots using relative positioning
        dots = VGroup(*[
            Dot().move_to(RIGHT * (i - 4.5) + DOWN * 0.5)
            for i in range(10)
        ])

        # Animate with LaggedStart
        self.play(LaggedStart(*[FadeIn(d) for d in dots], lag_ratio=0.1), run_time=2)
        self.wait(1)

        # Explicit cleanup
        self.play(FadeOut(c), FadeOut(dots))
```
```
