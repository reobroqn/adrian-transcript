# Adrian Cantrill Course Transcript VTT Collector via mitmproxy

## Overview
This project uses a `mitmproxy` addon to intercept and collect video transcript segments (`.webvtt` files) from network traffic, typically from video streaming platforms. It saves each VTT segment as a separate text file and provides a utility script to merge these files into a complete transcript.

## Project Structure

```
.
├── mitm_addon.py       # The main mitmproxy addon script that saves VTT segments as text files.
├── helpers.py          # Contains utility functions for parsing VTT and extracting video IDs.
├── make_transcripts.py  # Script to process VTT segments and create complete transcripts.
├── pyproject.toml       # Project metadata, dependencies, and Ruff linter configuration.
├── vtt_segments/        # Directory where individual VTT segments are saved as text files.
├── transcripts/         # Directory where final processed transcripts are saved.
└── README.md           # This file.
```

## How It Works

### Phase 1: Collecting VTT Segments

1. **Traffic Interception**: `mitmproxy` (with the `mitm_addon.py` script) is set up as a proxy to capture HTTP/S traffic.
2. **VTT Detection**: The addon inspects responses. If a response is for a `.webvtt` file and has a successful status code (200 OK), it's processed.
3. **URL Parsing**: Both the `video_id` and `filename` are extracted from the URL (e.g., from `https://example.com/video/GZWlDBXdRA/hls/GZWlDBXdRA-1723812919000-textstream_eng=1000-70.webvtt`).
4. **Saving Raw VTT**: The raw `.webvtt` content is saved as a text file in the `vtt_segments/{video_id}/` directory using the extracted filename to avoid duplicates.

### Phase 2: Processing Transcripts

1. **Segment Collection**: The `merge_transcripts.py` script scans the `vtt_segments/` directory for all segments belonging to a specific video ID.
2. **Parsing**: Each segment file is parsed to extract time ranges and content.
3. **Sorting**: All parsed segments are sorted chronologically based on their start time.
4. **Deduplication**: Duplicate segments are removed.
5. **Output**: The complete, sorted transcript is saved as a text file in the `transcripts_output/` directory.

## Sample `.webvtt` Response Snippet

The addon is designed to process `.webvtt` content like the following:

```webvtt
WEBVTT
Kind: captions
Language: en
X-TIMESTAMP-MAP=MPEGTS:900000,LOCAL:00:00:00.000

2184821625
00:06:54.000 --> 00:06:56.400
to connect with the application instance and for this example

331925936
00:06:56.400 --> 00:07:00.000
let's say this is using TCP port 1337

NOTE This is a comment and will be ignored.

00:07:01.000 --> 00:07:03.000
Another segment without an ID.
```

## Prerequisites

*   `uv` (for installing Python packages)

## Setup & Running

1. **Install dependencies**:
   Navigate to the project root directory (where `pyproject.toml` is located) and run:
   ```bash
   uv sync
   source .venv/Scripts/activate
   ```

2. **Run mitmproxy with the addon**:
   ```bash
   mitmweb -s mitm_addon.py
   ```

3. **Configure your browser/system to use mitmproxy**:
   By default, mitmproxy runs on `http://localhost:8080`. Configure your browser or system to use this as an HTTP/HTTPS proxy. You may also need to install the mitmproxy CA certificate in your browser/system to decrypt HTTPS traffic. Visit `http://mitm.it` while proxied through mitmproxy for certificate installation instructions.

4. **Process the collected VTT segments**:
   After collecting VTT segments by browsing videos through the proxy, run:
   ```bash
   python make_transcripts.py
   ```
   This will automatically process all available video IDs.

## Output

### VTT Segments
Raw VTT segments are saved as text files in the `vtt_segments/{video_id}/` directory, with filenames based on the original WebVTT URL.

### Final Transcripts
Processed transcripts are saved as text files in the `transcripts/` directory. Each file is named after the `video_id` (e.g., `GZWlDBXdRA.txt`).

Example transcript output format:
```
Welcome back and in this lesson I want to talk in detail about security groups within AWS.

These are the second type of security filtering feature commonly used within AWS. The other type being network access control lists which we've previously discussed.

So security groups and NACLs share many broad concepts but the way they operate is very different and it's essential that you understand those differences and the features offered by security groups for both the exam and real world usage.
```
