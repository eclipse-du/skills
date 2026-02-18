---
name: chinese-tts
description: Use when you need to convert Chinese text into speech (TTS) for ANY project using the designated `tts_tool.py`.
---

# Chinese Text-to-Speech (TTS)

## Overview

This skill provides a standardized way to generate high-quality Chinese speech audio using the `tts_tool.py` utility. It ensures consistent voice usage across different contexts and projects.

## When to Use

-   **Game Assets**: Generating character dialogue, narration, or system announcements.
-   **Voiceovers**: Creating audio tracks for videos or presentations.
-   **Notifications**: Generating system alerts or status updates.
-   **Prototyping**: Quickly creating placeholder audio for development.

## Voice Selection Guidelines

Choosing the right voice is critical for the user experience. Use the following mapping to select the appropriate voice for your context:

### Male Voices
| Voice ID | Style | Context / Scenario |
| :--- | :--- | :--- |
| `male_magnetic` | **Magnetic (Default)** | **General Assistant**, Protagonist, Friendly NPC, User Interaction. Default choice for most male roles. |
| `male_steady` | Steady / Calm | **News Reading**, Formal Announcements, Documentaries, Serious Narration. |
| `male_deep` | Deep | **Storytelling**, Movie Trailers, Mysterious Characters, Boss Battles. |
| `male_cheerful` | Cheerful | **Game Victory**, Happy Events, Comedic Characters, Energetic Announcements. |

### Female Voices
| Voice ID | Style | Context / Scenario |
| :--- | :--- | :--- |
| `female_gentle` | **Gentle (Default)** | **Customer Service**, Guide, Healing/Comforting Roles, Bedtime Stories. Default choice for most female roles. |
| `female_steady` | Steady / Calm | **News Broadcasting**, Corporate Presentations, Educational Content, Instructional Guides. |
| `female_passionate` | Passionate | **Dramatic Roles**, Intense Emotions, Urgent Alerts, Action Scenes. |
| `female_cheerful` | Cheerful | **Greetings**, Children's Content, Uplifting Messages, Social Media Shorts. |

> [!WARNING]
> **STRICT LIMITATION**: Only the 8 Voice IDs listed in the table above (`male_magnetic` ... `female_cheerful`) are allowed. Creating or using any other Voice ID (e.g., `zh-CN-XiaoxiaoNeural`) is strictly prohibited and will cause generation failure. You must strictly adhere to the mapping defined in `tts_tool.py`.

## Usage

Use the `run_command` tool to execute `tts_tool.py`.

```bash
python d:\du\code\user_projects\tools\tts_tool.py "<TEXT>" "<OUTPUT_FILE_PATH>" --voice <VOICE_ID> --format <FORMAT>
```

### Parameters

-   `<TEXT>`: The Chinese text to convert. Keep it concise for better results.
-   `<OUTPUT_FILE_PATH>`: Absolute path to save the audio file.
-   `--voice`: (Optional) The voice ID from the table above. Defaults to `male_magnetic`.
-   `--format`: (Optional) `mp3` (default) or `wav`. Use `wav` for lossless/editing, `mp3` for web/mobile.

## Best Practices

### 1. Naming Conventions
-   **Agent Decides**: You (the Agent) act as the creative director. Choose filenames that reflect the content and purpose.
-   **Format**: `snake_case` in **English**.
-   **Structure**: `[category]_[content_summary]_[voice_style]`.
    -   *Example*: `intro_welcome_message_female_gentle.mp3`
    -   *Example*: `level_1_victory_male_cheerful.wav`

### 2. Path Management
-   **Consistency**: Store audio files in a dedicated directory within the project, typically `assets/audio`, `resources/sound`, or `static/media`.
-   **Project Awareness**: Respect the existing project structure. If a fast-paced game, maybe `assets/sfx/voice`. If a web app, `public/audio`.

### 3. Workflow
1.  **Analyze Context**: Determine the emotion and purpose of the text.
2.  **Select Voice**: Choose the best fit from the Voice Guidelines table.
3.  **Generate**: Run the command.
4.  **Verify**: Check if the file was created successfully.
