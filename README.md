# Adrian Transcript Extractor

## Overview
This project uses Playwright to automatically extract full transcripts from Adrian courses by watching the video content programmatically.

## Authentication
The easiest way to handle authentication is by passing cookies to Playwright. This approach simplifies the authentication process by avoiding manual login steps.

**⚠️ Security Warning:**
Handle cookie information carefully as it contains sensitive authentication data. Never commit cookie information to version control or share it publicly.

## How It Works
1. The script uses Playwright to navigate to course pages
2. Authentication is handled via cookies
3. Videos are played and transcript content is extracted
4. Full transcripts are saved for later reference
