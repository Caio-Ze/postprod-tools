# PostProd Tools — Tool Reference

All tools follow the same conventions:

- **`--output-json`** flag on every invocation for structured output
- **`--session <path>`** to specify the Pro Tools session file
- **Absolute paths** with quoted spaces for all file arguments
- **Exit code 0** on success, non-zero on failure
- For tools with subcommands, `--output-json` must come **before** the subcommand

---

## Session Management

### agent-manage-tracks

Multi-purpose track management tool with subcommands:

| Subcommand | Description |
|------------|-------------|
| `list` | List all tracks in the session with type, index, and ID |
| `check` | Check if specific tracks exist |
| `markers` | List all memory locations (markers) |
| `create` | Create new tracks |
| `hide` | Show or hide tracks |
| `inactivate` | Activate or inactivate tracks |
| `solo` | Solo specific tracks (unsolo all others) |
| `select-clips` | Select all clips on a given track |

```
agent-manage-tracks --output-json --session <path> list
agent-manage-tracks --output-json --session <path> solo --tracks "Dialog_01,Dialog_02"
```

### agent-import-tracks

Import all tracks from a source session into the target session.

```
agent-import-tracks --output-json --session <target> --source <source>
```

### agent-import-trilha

Import a TRILHA (music/score) track from a source session.

```
agent-import-trilha --output-json --session <target> --source <source>
```

### agent-rename-track

Rename a track in the current session.

```
agent-rename-track --output-json --session <path> --track "Old Name" --new-name "New Name"
```

### agent-rename-clip

Rename a clip in the currently open session.

```
agent-rename-clip --output-json --clip "old_clip_name" --new-name "new_clip_name"
```

### agent-save-session-as

Save the current session under a new name or to a new location.

```
agent-save-session-as --output-json --session <path> --name "New Session Name" --directory <dir>
```

### agent-delete-tracks

Delete one or more tracks from the session.

```
agent-delete-tracks --output-json --session <path> --tracks "Track1,Track2"
```

### agent-get-clip-list

List all clips in the session.

```
agent-get-clip-list --output-json --session <path>
```

### agent-timeline-selection

Get or set the timeline in/out selection points.

```
agent-timeline-selection --output-json --session <path> get
agent-timeline-selection --output-json --session <path> set --in-time <samples> --out-time <samples>
```

### agent-transport

Control Pro Tools transport (play, stop, query status).

```
agent-transport --output-json --session <path> play
agent-transport --output-json --session <path> stop
agent-transport --output-json --session <path> status
```

---

## Mixer

### agent-mute-solo

Mute, unmute, solo, or unsolo tracks.

```
agent-mute-solo --output-json --session <path> mute --tracks "Track1,Track2"
agent-mute-solo --output-json --session <path> solo --tracks "Dialog_01"
```

### agent-track-volume

Set track fader volume in dB.

```
agent-track-volume --output-json --session <path> set --track-id <id> --volume -6.0
```

> Note: Uses track ID (not track name). Get track IDs from `agent-manage-tracks list`.

---

## Bounce & Export

### agent-bounce-export

Bounce the current session to WAV or MP3.

```
agent-bounce-export --output-json --session <path> --output <directory>
```

### agent-bounce-organize

Move or copy a bounced file to a central delivery folder.

```
agent-bounce-organize --output-json --input <file> --output <directory>
```

### agent-bounce-normalize-tv

Two-pass FFmpeg loudness normalization. Creates `_NET` (internet, -16 LUFS) and `_TV` (broadcast, -24 LUFS) variants.

```
agent-bounce-normalize-tv --output-json --input <wav_file>
```

### agent-export-loc

Export and consolidate a track to a standalone WAV file.

```
agent-export-loc --output-json --session <path> --track "Track Name" --output <directory>
```

### agent-maximize-audio

Peak-normalize audio to maximize volume without clipping.

```
agent-maximize-audio --output-json --input <file> --output <file>
```

### agent-convert-mp3

Convert audio files to MP3 (FFmpeg libmp3lame, VBR encoding).

```
agent-convert-mp3 --output-json --input <file> --output <file>
```

### agent-apply-audio-filter

Apply an arbitrary FFmpeg audio filter chain to a file.

```
agent-apply-audio-filter --output-json --input <file> --output <file> --filter "highpass=f=80,lowpass=f=12000"
```

---

## Import & Spot

### agent-import-audio

Import an audio file into the Pro Tools clip list.

```
agent-import-audio --output-json --session <path> --file <audio_file>
```

### agent-spot-clip

Spot a clip to a specific track at a precise sample position.

```
agent-spot-clip --output-json --session <path> --clip "clip_name" --track "Track Name" --position <samples>
```

### agent-create-markers

Create session markers from a markdown timestamps file.

```
agent-create-markers --output-json --session <path> --file <timestamps.md>
```

### agent-copy-markers

Copy all markers from a source session to the target session.

```
agent-copy-markers --output-json --session <target> --source <source>
```

### agent-version-match

Find versioned tracks (V1, V2, etc.) and report their version numbers.

```
agent-version-match --output-json --session <path>
```

---

## Transcription & QC

### agent-transcribe-audio

Transcribe audio using the Groq Whisper API.

```
agent-transcribe-audio --output-json --input <audio_file>
```

> Requires `GROQ_API_KEY` environment variable.

### agent-extract-text

Extract plain text from documents (PDF, DOCX, RTF, etc.) using macOS textutil.

```
agent-extract-text --output-json --input <document>
```

### agent-compare-texts

Compare a reference script against a transcription for quality control. Returns a diff-style comparison with match percentage.

```
agent-compare-texts --output-json --reference <text_file> --transcription <text_file>
```

> Requires `GROQ_API_KEY` environment variable.

---

## JSON Output Format

All tools return JSON in this structure:

```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": { ... }
}
```

On failure:

```json
{
  "success": false,
  "error": "Description of what went wrong"
}
```

Always check `"success": true` before proceeding in automation pipelines.
