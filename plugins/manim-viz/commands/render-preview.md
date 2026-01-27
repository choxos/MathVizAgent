---
name: render-preview
description: Provide rendering commands and preview options for Manim animations
---

# /render-preview - Rendering Commands Reference

Quick reference for rendering Manim animations at various quality levels.

## Basic Render Commands

### Quality Presets

```bash
# Low quality (480p15) - Quick preview
manim -pql scene_file.py SceneName

# Medium quality (720p30) - Testing
manim -pqm scene_file.py SceneName

# High quality (1080p60) - Production
manim -pqh scene_file.py SceneName

# 4K quality (2160p60) - Highest quality
manim -pqk scene_file.py SceneName
```

### Quality Preset Details

| Flag | Resolution | FPS | Use Case |
|------|------------|-----|----------|
| `-ql` | 480p | 15 | Quick preview |
| `-qm` | 720p | 30 | Testing, review |
| `-qh` | 1080p | 60 | Production |
| `-qk` | 2160p | 60 | 4K displays |

## Common Options

### Preview Options

```bash
# -p: Preview after render (opens video player)
manim -pql scene.py MyScene

# Without preview (just render)
manim -ql scene.py MyScene

# Preview specific section
manim -pql --from_animation_number 5 scene.py MyScene
```

### Output Options

```bash
# Custom output file name
manim -pqm -o custom_name.mp4 scene.py MyScene

# Specify output directory
manim -pqm --media_dir ./output scene.py MyScene

# Transparent background (for compositing)
manim -pqm -t scene.py MyScene  # Creates .mov with alpha

# GIF output
manim -pqm --format gif scene.py MyScene
```

### Performance Options

```bash
# Disable caching (when debugging)
manim -pql --disable_caching scene.py MyScene

# Force re-render
manim -pql --flush_cache scene.py MyScene

# Verbose output (debugging)
manim -pql -v DEBUG scene.py MyScene

# Quiet output
manim -pql -v WARNING scene.py MyScene
```

## Render All Scenes

```bash
# Render all scenes in a file
manim -qm scene_file.py -a

# Render all scenes with preview
manim -pqm scene_file.py -a

# Render specific scenes
manim -qm scene_file.py Scene1 Scene2 Scene3
```

## Section-Based Rendering

If scenes use `next_section()`:

```bash
# Save each section as separate file
manim -pql --save_sections scene.py MyScene

# Skip to specific section
manim -pql -n 0,2 scene.py MyScene  # Sections 0-2
```

## Video Concatenation

After rendering multiple scenes:

```bash
# Create filelist.txt
cat > filelist.txt << 'EOF'
file 'Scene1.mp4'
file 'Scene2.mp4'
file 'Scene3.mp4'
EOF

# Concatenate without re-encoding (fast, same codec)
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4

# Concatenate with re-encoding (consistent quality)
ffmpeg -f concat -safe 0 -i filelist.txt -c:v libx264 -crf 18 output.mp4

# Concatenate with specific settings
ffmpeg -f concat -safe 0 -i filelist.txt \
  -c:v libx264 -preset slow -crf 18 \
  -c:a aac -b:a 192k \
  output.mp4
```

## Custom Resolution

```bash
# Specify exact resolution
manim -pqm --resolution 1920,1080 scene.py MyScene

# Custom frame rate
manim -pqm --fps 24 scene.py MyScene

# Portrait mode (vertical video)
manim -pqm --resolution 1080,1920 scene.py MyScene
```

## Rendering Tips

### For Development

```bash
# Quick iteration cycle
manim -pql --disable_caching scene.py MyScene
```

### For Review

```bash
# Medium quality, fast enough to review
manim -pqm scene.py MyScene
```

### For Production

```bash
# High quality, all scenes
manim -qh scene_file.py -a

# Then concatenate
cd media/videos/scene_file/1080p60/
ffmpeg -f concat -safe 0 -i filelist.txt -c copy ../../final.mp4
```

### For Social Media

```bash
# Instagram (square)
manim -pqm --resolution 1080,1080 scene.py MyScene

# TikTok/Reels (vertical)
manim -pqm --resolution 1080,1920 scene.py MyScene

# Twitter (landscape)
manim -pqm --resolution 1280,720 scene.py MyScene
```

## Troubleshooting

### Render Takes Too Long

```bash
# Use lower quality for testing
manim -pql scene.py MyScene

# Check for expensive operations
manim -pql -v DEBUG scene.py MyScene
```

### Out of Memory

```bash
# Reduce resolution
manim -pql --resolution 640,360 scene.py MyScene

# Clear cache
manim --flush_cache scene.py MyScene
```

### Video Player Doesn't Open

```bash
# Render without preview, then open manually
manim -ql scene.py MyScene
# Output is in media/videos/scene/480p15/
```

### LaTeX Errors

```bash
# Check LaTeX installation
manim --check

# Use verbose mode to see LaTeX errors
manim -pql -v DEBUG scene.py MyScene
```

## Output Directory Structure

```
media/
├── videos/
│   └── scene_file/
│       ├── 480p15/       # -ql output
│       │   └── MyScene.mp4
│       ├── 720p30/       # -qm output
│       │   └── MyScene.mp4
│       └── 1080p60/      # -qh output
│           └── MyScene.mp4
├── images/               # Still frames
└── Tex/                  # LaTeX cache
```

## Quick Reference Card

```
RENDER:     manim -pqm scene.py Scene
ALL:        manim -qm scene.py -a
PREVIEW:    manim -pql scene.py Scene
PRODUCTION: manim -qh scene.py -a
GIF:        manim -qm --format gif scene.py Scene
CONCAT:     ffmpeg -f concat -safe 0 -i filelist.txt -c copy out.mp4
```
