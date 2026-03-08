# Funclip Commander for OpenClaw

**AI-powered video recognition and clipping via FunClip.**

## Quick start

```bash
# Install to OpenClaw skills directory
git clone https://github.com/RuneweaverStudios/funclip-commander.git
cp -r funclip-commander ~/.openclaw/workspace/skills/

# Recognize speech in a video
python3 ~/.openclaw/workspace/skills/funclip-commander/scripts/funclip_commander.py \
  recognize_video ./my_video.mp4 ./output/

# Clip a segment by text
python3 ~/.openclaw/workspace/skills/funclip-commander/scripts/funclip_commander.py \
  clip_video ./my_video.mp4 ./output/ \
  --srt_file_path ./output/my_video.srt \
  --dest_text "the part I want to clip"
```

## Features

- **Speech recognition** -- Transcribe audio from video files to SRT subtitles
- **Video clipping** -- Extract segments by text match, timestamps, or speaker
- **Multiple ASR backends** -- Buzz (offline/local), OpenAI Whisper (cloud), or FunASR (FunClip native)
- **Configurable language** -- Set language in `config.json` (default: `en`)
- **YouTube support** -- Accepts YouTube URLs directly (requires `yt-dlp`)

## Prerequisites

1. **FunClip** cloned and installed with its dependencies in a virtualenv
2. **ffmpeg** and **ffprobe** on PATH
3. **yt-dlp** on PATH (for YouTube URLs)
4. **imagemagick** (optional, for embedded subtitles)

## Configuration

Edit `config.json` to set paths and ASR backend:

```json
{
  "funclip_path": "${OPENCLAW_HOME}/workspace/ClawClip",
  "venv_path": "${OPENCLAW_HOME}/workspace/ClawClip/.venv",
  "language": "en",
  "use_buzz_for_recognition": true,
  "buzz_python": "${SKILL_DIR}/scripts/buzz_venv/bin/python3",
  "buzz_model": "tiny.en",
  "use_whisper_for_recognition": false,
  "whisper_model": "whisper-1"
}
```

All paths support `${OPENCLAW_HOME}` and `${SKILL_DIR}` placeholder expansion.

### Optional: Buzz for offline ASR

```bash
bash scripts/install_buzz_venv.sh
```

This creates a Python 3.12 virtualenv at `scripts/buzz_venv/` with `buzz-captions`. Requires Python 3.12 (not 3.13+).

See `SKILL.md` for the full command reference and backend details.

## License

MIT
