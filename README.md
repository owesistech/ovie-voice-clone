# Thalika

Thalika is a local-first voice cloning and voice-over studio built with Next.js, TypeScript, TailwindCSS, and an optional Electron desktop shell.

All scripts, generation jobs, generated audio files, and draft states are stored locally in the `data/` directory. No database is used.

## Features

* AI-powered script rewriting using Google Gemini
* Voice-over generation with reference voice cloning
* Burmese Production mode powered by VoxCPM2
* Multilingual voice synthesis support
* Validated 48kHz mono 24-bit PCM WAV output
* Intelligent long-script chunking and WAV merging
* Audio preview and download
* Generation history and quality review system
* Local voice profiles with explicit user consent
* Custom Burmese pronunciation lexicon
* Browser-side reference audio quality validation
* Local storage management and WAV migration tools
* Optional Electron desktop application

## Technology Stack

### Frontend

* Next.js App Router
* React
* TailwindCSS

### Backend

* Next.js Route Handlers

### Desktop

* Electron

### Validation

* Zod

### Storage

* Local Markdown files
* JSON files
* Audio files

### AI Providers

* VoxCPM2 (Voice Generation)
* Google Gemini (Script Rewriting)

## Requirements

* Node.js 22.12.0 or newer
* npm
* Internet connection (required for VoxCPM2 and Gemini services)

VoxCPM2 inference runs through a remote Hugging Face Space. Thalika does not install or host a local VoxCPM2 model.

## Installation

```bash
npm install
```

## Environment Variables

Create a local environment file:

```bash
cp .env.example .env.local
```

Example configuration:

```env
HF_VOXCPM2_URL=https://openbmb-voxcpm-demo.hf.space
HF_REQUEST_TIMEOUT=60000
HF_INFERENCE_TIMEOUT=300000
GEMINI_REQUEST_TIMEOUT=60000
GEMINI_API_KEY=your_google_gemini_api_key_here
```

## Running the Application

### Development Mode

```bash
npm run dev
```

### Production Mode

```bash
npm run build
npm run start
```

Open:

```text
http://localhost:3000
```

## Electron Desktop Mode

### Development

```bash
npm run desktop
```

### Production

```bash
npm run build
npm run desktop:start
```

Security settings include:

* nodeIntegration: false
* contextIsolation: true
* sandbox: true
* webSecurity: true
* Single-instance lock

## Main Application Pages

### Script Page

The Script page allows users to:

* Paste an original script
* Rewrite content using Gemini
* Select a Gemini model
* Preserve Burmese language when required
* Save rewritten content for voice-over generation

### Voice Over Page

The Voice Over page allows users to:

* Generate voice-over audio
* Upload reference voice samples
* Use saved voice profiles
* Configure cloning settings
* Review pronunciation previews
* Download generated audio

Supported providers:

| Provider             | Description                             |
| -------------------- | --------------------------------------- |
| Burmese Production   | Burmese-optimized profile using VoxCPM2 |
| VoxCPM2 Multilingual | Direct multilingual voice generation    |

### History Page

Users can:

* View generation history
* Play generated audio
* Download files
* Delete jobs
* Submit QA reviews
* Mark jobs as approved or needing review

### Folders Page

Users can:

* Browse managed storage folders
* Review storage statistics
* Open managed folders
* Migrate legacy audio files to PCM WAV

## Validation Rules

| Field           | Rule                               |
| --------------- | ---------------------------------- |
| Title           | Optional, max 100 characters       |
| Script          | Required, 10–50,000 characters     |
| Provider        | voxcpm2 or burmese_production      |
| Output Format   | WAV only                           |
| Speed           | 0.8x–1.2x                          |
| Emotion         | neutral, calm, energetic, dramatic |
| Clone Mode      | balanced or high_fidelity          |
| Clone Strength  | 1.0–3.0                            |
| Reference Audio | Required                           |
| Audio Size      | Maximum 10 MB                      |
| Audio Duration  | 3–50 seconds                       |

## Long Script Processing

Long scripts are automatically split into chunks.

### Pause Rules

| Punctuation | Pause |
| ----------- | ----- |
| . ! ? ။     | 260ms |
| , ; : ၊     | 160ms |
| None        | 120ms |

All chunks are merged into a single validated 48kHz mono 24-bit PCM WAV master file.

## Local Storage Structure

```text
data/
├── scripts/
├── jobs/
├── outputs/
│   └── legacy-backup/
├── profiles/
├── reviews/
└── memory/
    ├── MEMORY.md
    ├── MEMORY.example.md
    ├── voice-over-draft.json
    └── burmese-lexicon.json
```

## API Routes

| Method          | Route                           | Purpose                      |
| --------------- | ------------------------------- | ---------------------------- |
| GET             | /api/health                     | Service health check         |
| POST            | /api/generate                   | Generate audio               |
| GET             | /api/audio/{filename}           | Stream generated audio       |
| GET             | /api/history                    | Retrieve generation history  |
| DELETE          | /api/history/{jobId}            | Delete generation job        |
| PUT             | /api/history/{jobId}/review     | Save QA review               |
| GET             | /api/scripts                    | Retrieve saved scripts       |
| GET             | /api/providers/capabilities     | Provider capabilities        |
| GET             | /api/providers/voxcpm2/health   | VoxCPM2 health status        |
| POST            | /api/rewrite                    | Rewrite script using Gemini  |
| GET/POST        | /api/settings/gemini            | Manage Gemini settings       |
| GET/POST/DELETE | /api/drafts/voice-over          | Manage voice-over drafts     |
| POST            | /api/burmese/normalize          | Pronunciation preview        |
| GET/PUT         | /api/settings/burmese-lexicon   | Manage pronunciation lexicon |
| GET/POST        | /api/voice-profiles             | Manage local voice profiles  |
| DELETE          | /api/voice-profiles/{profileId} | Delete voice profile         |
| GET             | /api/storage/local              | Storage inspection           |
| GET/POST        | /api/storage/migrate-wav        | WAV migration                |

## Security

* Voice profiles are stored only after explicit user consent.
* Generated filenames are sanitized.
* Audio access is restricted to managed storage directories.
* Folder access is limited to predefined locations.
* No shell commands are executed during inference.
* Electron runs with sandbox and isolation enabled.

## Health Monitoring

VoxCPM2 health statuses:

* HF connected
* HF timeout
* HF rate limited
* HF unavailable
* HF invalid response

## Resource Commands

Memory usage:

```bash
npm run metrics:memory
```

CPU and RAM usage:

```bash
npm run metrics:resources
```

## Verification Commands

Linting:

```bash
npm run lint
```

Production build:

```bash
npm run build
```

## Troubleshooting

### Audio Generation Issues

Verify:

* Script length is valid
* Reference audio is uploaded
* Audio size and duration meet requirements
* Internet connectivity is available

### VoxCPM2 Issues

Check:

* HF_VOXCPM2_URL
* HF_REQUEST_TIMEOUT
* HF_INFERENCE_TIMEOUT
* Generation logs

### Gemini Issues

Check:

* GEMINI_API_KEY
* GEMINI_REQUEST_TIMEOUT
* Model availability

### Electron Issues

Repair Electron cache:

```bash
npm run repair:electron
```

## Voice Cloning Policy

Voice cloning should only be performed using:

1. Your own voice, or
2. A voice for which you have obtained explicit permission from the original owner.

Users are solely responsible for any legal, ethical, or policy violations arising from unauthorized voice cloning.
