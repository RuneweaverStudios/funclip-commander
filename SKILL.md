---
name: funclip-commander
displayName: Funclip Commander
description: AI-powered video recognition and clipping via FunClip. Supports Buzz (offline), OpenAI Whisper (cloud), and FunASR backends with configurable language.
version: 0.2.0
---

# Funclip Commander Skill

This skill allows OpenClaw agents to leverage the powerful features of the open-source FunClip AI video editor. By utilizing FunClip's command-line interface, agents can:

- Perform speech recognition on video files.
- Generate SRT subtitles.
- Clip specific segments of videos based on text prompts or speaker diarization.

## Purpose

The Funclip Commander skill acts as a bridge between OpenClaw's agentic capabilities and FunClip's sophisticated video processing. It enables automated, AI-driven video content creation and analysis workflows directly from your OpenClaw environment.

## Prerequisites

1.  **FunClip Installed Locally**: The FunClip repository (`https://github.com/modelscope/FunClip`) must be cloned to your OpenClaw workspace within a directory named `FunClip`. All its Python dependencies must be installed in a virtual environment (`FunClip/.venv`).
2.  **`ffmpeg` and `ffprobe`**: These must be available in the system's PATH.
3.  **`imagemagick`**: (Optional, for embedded subtitles) Must be installed and configured as per FunClip's `README.md`.
4.  **`yt-dlp`**: Required for downloading YouTube videos. Must be available in the system’s PATH.

### Buzz Setup (optional, for offline ASR)

The `scripts/install_buzz_venv.sh` script creates a dedicated Python 3.12 virtualenv (`scripts/buzz_venv/`) and installs `buzz-captions`. Run it once to enable the Buzz offline Whisper backend. The script automatically updates `config.json` with the path (if `jq` is installed).

## Usage

This skill will expose functions to:

- `funclip.recognize_video(file_path_or_url: str, output_dir: str)`: Handles video download (if YouTube URL), extracts audio, and performs speech-to-text transcription. Depending on `config.json` settings, it uses Buzz (offline), OpenAI Whisper (cloud), or ClawClip's native ASR. Returns `(video_filepath: str, audio_filepath: str, srt_filepath: str, srt_content: str)`.
- `funclip.clip_video(file_path_or_url: str, output_dir: str, srt_file_path: str, dest_text: str = None, start_ost: int=0, end_ost: int=0, output_file: str = None)`: Clips video based on a provided SRT, recognized text, or timestamps using ClawClip's Stage 2.

## Configuration (`config.json`)

- **funclip_path**: Path to the ClawClip repo. Supports `${OPENCLAW_HOME}` and `${SKILL_DIR}` placeholders for portability (e.g. `${OPENCLAW_HOME}/workspace/ClawClip`).
- **venv_path**: Path to ClawClip's virtual environment (e.g. `${OPENCLAW_HOME}/workspace/ClawClip/.venv`).
- **use_buzz_for_recognition**: `true` to use Buzz for ASR (default if key/path provided). Requires `buzz_python`.
- **buzz_python**: Path to the Python executable within Buzz's virtual environment (e.g., `${SKILL_DIR}/scripts/buzz_venv/bin/python3`). Supports `${SKILL_DIR}` and `${OPENCLAW_HOME}` placeholders. Buzz 1.4.x requires Python 3.12 or older.
- **use_whisper_for_recognition**: `true` to use OpenAI Whisper API for ASR. Conflicts with `use_buzz_for_recognition`.
- **whisper_model**: Whisper model to use (default: `whisper-1`). Requires `OPENAI_API_KEY` to be configured in Gateway.
- **language**: ISO 639-1 language code for ASR (default: `en`). Used by both Buzz and FunClip native backends.
- **buzz_model**: Whisper model size for Buzz (default: `tiny.en`). Use `tiny`, `base`, `small`, `medium`, or `large` for multilingual support.

Recognition Backends (selected via config, `buzz` has priority over `whisper`):

*   **Buzz (offline)**: Requires a Python 3.12 environment with Buzz installed. Ensures privacy and local processing. Configure `buzz_python` and set `use_buzz_for_recognition: true`.
*   **OpenAI Whisper API (cloud)**: Requires an `OPENAI_API_KEY` to be configured in the OpenClaw Gateway. Best for accuracy and convenience if API access is available. Set `use_whisper_for_recognition: true`.
*   **ClawClip Native (FunASR)**: Fallback to ClawClip's built-in ASR if neither Buzz nor Whisper are configured or available.

## Commands

### Recognize video (speech-to-text)

```bash
python3 <skill-dir>/scripts/funclip_commander.py recognize_video <file_or_url> <output_dir> [--whisper] [--buzz]
```

- `<file_or_url>` — Local video file path or YouTube URL.
- `<output_dir>` — Directory for output audio (.wav) and subtitles (.srt).
- `--whisper` — Force OpenAI Whisper API backend (overrides config).
- `--buzz` — Force Buzz offline backend (overrides config).

### Clip video

```bash
python3 <skill-dir>/scripts/funclip_commander.py clip_video <file_or_url> <output_dir> --srt_file_path <srt> [--dest_text "text"] [--start_ost 0] [--end_ost 0] [--output_file clip.mp4]
```

- `--srt_file_path` — Path to SRT subtitle file (from recognize step).
- `--dest_text` — Text segment to search for and clip.
- `--start_ost` / `--end_ost` — Start/end offset in milliseconds.
- `--output_file` — Custom output filename.

### Install Buzz venv

```bash
bash <skill-dir>/scripts/install_buzz_venv.sh
```

Creates a Python 3.12 virtualenv at `<skill-dir>/scripts/buzz_venv/` with `buzz-captions` installed. Updates `config.json` with the correct `buzz_python` path (requires `jq`).
