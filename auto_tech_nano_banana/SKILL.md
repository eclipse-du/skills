---
name: Auto-Tech-Nano-Banana
description: Analyzes the codebase and uses AI image generation to create professional technical presentation slides (images).
---

# Auto-Tech-Nano-Banana

## Purpose
To automatically generate high-quality visual "slides" (images) that summarize the project's value, architecture, and technology stack. This is useful for quick reporting and sharing technical overviews.

## Agent Instructions

**Trigger**: When the user asks for "banana report", "slides", "project overview images", or explicitly invokes the skill.

### 1. Analysis Phase
*   **Scan the Codebase**: Read `README.md`, architecture docs, `build.gradle`/`package.json`, and core source files.
*   **Extract Key Info**:
    *   **Value**: What does this project do? Who is it for? Key features?
    *   **Architecture**: How is it organized? (MVVM, Clean Arch, etc.) Data flow?
    *   **Tech Stack**: Languages, frameworks, key libraries.

### 2. Generation Phase (Image Generation)
You MUST generate exactly 3 images using the `generate_image` tool. 

**Common Style Rules**:
*   **Style**: Professional execution, flat design, modern UI/UX presentation style.
*   **Aspect Ratio**: 16:9 (Landscape).
*   **Visuals**: Clean typography, schematic diagrams, icons.

#### Slide 1: Project Value
*   **Filename**: `project_value`
*   **Content**: Title of the project, tagline, 3-4 bullet points on key benefits/features.
*   **Prompt Strategy**: "A presentation slide titled '[Project Name]'. Modern design. Bullet points: [Benefit 1], [Benefit 2]..."

#### Slide 2: Project Architecture
*   **Filename**: `project_architecture`
*   **Content**: A high-level block diagram or flow chart visualizing the system.
*   **Prompt Strategy**: "A technical architecture diagram for [Project]. Layers: [Layer 1], [Layer 2]. Arrows showing data flow. Professional schematic style."

#### Slide 3: Tech Stack
*   **Filename**: `tech_stack`
*   **Content**: Logos or lists of technologies used (e.g., Kotlin, Jetpack Compose, Gemini).
*   **Prompt Strategy**: "A presentation slide displaying a technology stack. Icons/Text for: [Tech 1], [Tech 2], [Tech 3]. Grid layout, clean background."

### 3. Output Rules
*   **Save Location**: You **MUST** save all generated images to `[ProjectRoot]/img_ai_gen/report/`.
*   **Naming**: Ensure filenames match the descriptions above (`project_value`, `project_architecture`, `tech_stack`).
