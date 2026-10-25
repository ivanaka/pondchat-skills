---
name: join-pond
description: Join an existing Pondchat room by ID or full URL.
---

# Join Pond Room

Use when the user has a pond link or room ID.

## URL format

- `https://pondchat.com/{roomId}`
- Room ID may be extracted from pasted URLs.

## Steps

1. Parse room ID from input.
2. Navigate to `https://pondchat.com/{encodeURIComponent(roomId)}`.
3. Grant camera/microphone when prompted.

## WebMCP

On https://pondchat.com/, call the `join_pond` tool with `{ "roomId": "..." }`.

## Signaling

WebSocket: `wss://pondchat.com/api/room/{roomId}`

The upgrade requires an allowlisted `Origin` header (CSWSH check). Non-browser agents must send `Origin: https://pondchat.com` — without it the handshake returns `403`.

## Reading chat (MCP, no WebSocket needed)

`POST https://pondchat.com/api/mcp` with `tools/call` → `get_messages` `{ "roomId": "...", "limit": 50, "since": "<ISO-8601>" }` returns recent chat **decrypted** (the roomId is the read capability — moderated/locked rooms reject it). Poll with `since` = the last item's `receivedAt`. Pair with `send_message` for a full agent loop without a browser.

### Media (audio/video)

To participate with audio or video, join as a **regular WebRTC peer** per `docs/api/signaling.md` — open the signaling WebSocket (with the `Origin` header), send `join`, exchange `offer`/`answer`/`candidates-batch` with existing peers, and publish a `MediaStreamTrack`. Media is P2P only (DTLS-SRTP, mesh of ≤6); there is no server media path and MCP cannot carry it. A WebRTC stack (browser, `werift`, `aiortc`, `pion`) is required — headless Chromium with a virtual camera works.
