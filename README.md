# Drive video player

Public static player: https://mrbahar666.github.io/drive-video-player/

## Current status

Playback **verified on desktop Chromium 2026-11-07**: the full 2h 40m file plays from the Pages frontend, time advances in real time, and a 5-minute seek works (lands ~14 s past the target at keyframe granularity). The original Google Drive download endpoint rejected cross-origin browser requests during testing (CORP: same-site on Drive responses), so the player streams through a fixed-file proxy at `https://www.shizenspa.com/drive-video` (Shizen VPS nginx, this one public Drive file only, Pages-only CORS, rate-limited). No API credential has been created, collected, or included. Audio renders (2 channels, 48 kHz, 0 decode errors) but audible sound was not confirmed in the headless test browser; iOS/Safari/Firefox and audio-track switching are still untested.

## Implementation

- Pinned libmedia 1.3.1 player with HEVC and AAC WebAssembly decoders.
- Original MKV remains on Google Drive; the repository does not store the original video.
- No backend, analytics, payments, or third-party public CORS proxy.
- Requires a compatible browser, including the rendering/audio features required by libmedia. Device performance and Drive quotas affect playback.
- The Shizen proxy forwards byte ranges to Drive and strips Google's browser-blocking headers (including the `Sec-Fetch-*` headers that made Google return 403); playback and seeking are verified on desktop Chromium from the Pages origin.

## Safety and configuration

Never commit OAuth tokens, client secrets, service-account credentials, unrestricted API keys, or private account credentials. An eventual browser-client key must be explicitly authorized and restricted to this website and Google Drive API. No credential entry form is implemented.

## Files

- `index.html`: player and bounded source diagnostic.
- `decoder-check.html`: isolated synthetic HEVC/AAC decoder diagnostic.
- `decoder-test-data.js`: self-generated three-second test pattern, unrelated to the original video.
- `THIRD_PARTY_NOTICES.md`: open-source attribution and license/source links.

## Verification still required

1. Proxy route is live (2026-11-07): range, CORS, and guard checks pass via curl; browser playback verified on desktop Chromium.
2. Small media range returns actual MKV bytes in the browser (Check source: HTTP 206, EBML header) — verified.
3. Video and audio frames advance, pause/resume, distant seeking, audio selection, and a mobile layout — video advance + 5-minute seek verified; pause/resume, audio-track switching, and mobile layout still untested.
4. Audible audio on a real device, iOS/Safari/Firefox playback, and audio-track switching still need real-device tests.

Google documents [API-key access to public Workspace data](https://developers.google.com/workspace/guides/create-credentials#api_key_credentials) and [Drive partial downloads](https://developers.google.com/workspace/drive/api/guides/manage-downloads#partial_download).
