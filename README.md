<p align="center">
  <img src="assets/logo.png" alt="PostProd Tools" width="480">
</p>

<p align="center">
  <strong>AI-driven automation for Pro Tools audio post-production</strong>
</p>

<p align="center">
  <a href="#tools">Tools</a> &middot;
  <a href="docs/tool-reference.md">Reference</a> &middot;
  <a href="#how-it-works">How It Works</a> &middot;
  <a href="#licensing">Licensing</a>
</p>

---

## What is PostProd Tools?

PostProd Tools is a suite of **31 command-line tools** that automate Pro Tools workflows via the PTSL (Pro Tools Scripting Language) protocol. Each tool performs a single, well-defined operation — from importing tracks and spotting clips to bouncing mixes and normalizing loudness.

They are designed to be composed together, driven by AI agents, or triggered from the [ProTools Studio IDE](https://github.com/Caio-Ze/protools-studio) dashboard.

**The result:** workflows that used to take a team of editors hours now run in minutes, unattended.

## The Problem

Audio post-production studios running Pro Tools face the same bottleneck: highly repetitive, error-prone manual work.

- Importing dozens of tracks from source sessions
- Renaming, organizing, and consolidating clips
- Bouncing multiple formats with correct loudness standards
- Versioning sessions across delivery stages
- Spotting audio to precise timecode positions

These tasks consume hours of skilled editor time per project. Scaling means hiring more people to do the same repetitive work.

## The Solution

PostProd Tools replaces manual repetition with deterministic automation:

- **One command** imports all tracks from a source session
- **One command** bounces and normalizes to broadcast loudness standards (LUFS)
- **One command** spots audio clips to exact timecode positions
- **AI agents** chain tools together to handle complete workflows end-to-end

Every tool outputs structured JSON, making them composable with scripts, AI agents, or any automation pipeline.

## Tools

### Session Management
| Tool | Description |
|------|-------------|
| `agent-manage-tracks` | List, create, hide, inactivate, solo tracks; manage markers; select clips |
| `agent-import-tracks` | Import tracks from a source Pro Tools session |
| `agent-import-trilha` | Import music (TRILHA) tracks from source |
| `agent-rename-track` | Rename a track in the current session |
| `agent-rename-clip` | Rename a clip in the current session |
| `agent-delete-tracks` | Delete one or more tracks |
| `agent-save-session-as` | Save session under a new name or location |
| `agent-get-clip-list` | List all clips in the session |
| `agent-timeline-selection` | Get or set timeline in/out points |
| `agent-transport` | Play, stop, and query transport state |

### Mixer
| Tool | Description |
|------|-------------|
| `agent-mute-solo` | Mute, unmute, solo, or unsolo tracks |
| `agent-track-volume` | Set track fader volume in dB |

### Bounce & Export
| Tool | Description |
|------|-------------|
| `agent-bounce-export` | Bounce session to WAV or MP3 |
| `agent-bounce-organize` | Move bounced files to a central delivery folder |
| `agent-bounce-normalize-tv` | Two-pass loudness normalization (creates broadcast-ready variants) |
| `agent-export-loc` | Export and consolidate a track to WAV |
| `agent-maximize-audio` | Peak-normalize audio to maximize volume |
| `agent-convert-mp3` | Convert audio files to MP3 |
| `agent-apply-audio-filter` | Apply FFmpeg audio filter chains |

### Import & Spot
| Tool | Description |
|------|-------------|
| `agent-import-audio` | Import audio files into the Pro Tools clip list |
| `agent-spot-clip` | Spot a clip to a track at a precise sample position |
| `agent-create-markers` | Create session markers from a timestamps file |
| `agent-copy-markers` | Copy markers from one session to another |
| `agent-version-match` | Find and report versioned tracks |

### Transcription & QC
| Tool | Description |
|------|-------------|
| `agent-transcribe-audio` | Transcribe audio using AI (Groq Whisper) |
| `agent-extract-text` | Extract plain text from documents |
| `agent-compare-texts` | Compare reference script against transcription for QC |

## How It Works

```
Pro Tools  <──gRPC──>  PTSL Agent Tools  <──JSON──>  AI Agent / Script / Dashboard
   │                        │
   │  PTSL Protocol         │  Structured output
   │  (localhost:31416)     │  (--output-json)
   │                        │
   ▼                        ▼
 Session state         Deterministic results
 Track data            Error reporting
 Transport control     Composable pipelines
```

Each tool:
1. Connects to Pro Tools via the PTSL gRPC protocol (localhost)
2. Executes a single, well-defined operation
3. Returns structured JSON with success/failure status
4. Exits with appropriate error codes for pipeline integration

Tools are designed to be **stateless and composable** — chain them together for complex workflows.

## Example Workflows

**Import tracks from a source session, solo only what you need:**
```bash
agent-import-tracks --output-json --session "$TARGET" --source "$SOURCE"
agent-mute-solo --output-json --session "$TARGET" solo --tracks "Dialog_01,Dialog_02"
```

**Bounce and normalize for broadcast delivery:**
```bash
agent-bounce-export --output-json --session "$SESSION" --output "$BOUNCE_DIR"
agent-bounce-normalize-tv --output-json --input "$BOUNCE_DIR/mix.wav"
```

**Spot audio to a precise timecode position:**
```bash
agent-import-audio --output-json --session "$SESSION" --file "$AUDIO_FILE"
agent-spot-clip --output-json --session "$SESSION" --clip "voiceover_01" --track "VO" --position 48000
```

## Requirements

- **macOS 12.0+** (Apple Silicon or Intel)
- **Pro Tools 2025.3+** with PTSL enabled
- Pro Tools must be running with a session open
- Network access to `localhost:31416` (PTSL default port)

## Companion: ProTools Studio IDE

PostProd Tools integrates with [**ProTools Studio**](https://github.com/Caio-Ze/protools-studio), a native macOS audio post-production IDE built on the GPUI framework. The IDE provides:

- A visual dashboard with one-click tool execution
- AI agent integration for natural-language workflow automation
- Real-time session monitoring

## Licensing

PostProd Tools is **proprietary commercial software**. The tools are distributed as compiled binaries under a commercial license.

For licensing inquiries, pricing, and demo access:

**Email:** [caioraphael@me.com](mailto:caioraphael@me.com)
**GitHub:** [@Caio-Ze](https://github.com/Caio-Ze)

---

<p align="center">
  <sub>Built with Rust. Powered by the PTSL protocol. Designed for audio professionals.</sub>
</p>
