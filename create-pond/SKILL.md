---
name: create-pond
description: Create a new Pondchat video room and return the shareable URL.
---

# Create Pond Room

Use when the user or workflow needs a new ephemeral video chat room.

## Preferred: HTTP mint (agents)

```http
POST https://pondchat.com/api/agent/pond
Content-Type: application/json

{}
```

Optional JSON body:

```json
{
  "roomId": "optional-custom-id",
  "source": "your-agent-name",
  "label": "opaque ticket id"
}
```

**201 response:**

```json
{
  "roomId": "V1StGXR8_Z5j",
  "roomUrl": "https://pondchat.com/V1StGXR8_Z5j",
  "websocketUrl": "wss://pondchat.com/api/room/V1StGXR8_Z5j",
  "maxParticipants": 6,
  "lifecycle": {
    "chatRetention": "wiped_when_empty",
    "mediaStorage": "none",
    "door": "reopenable_web"
  }
}
```

Give humans `roomUrl`. They open it in a browser (camera/mic consent). Rate limit: 10 mints / 60s / IP. CORS: `*`. Prefer apex `pondchat.com` (www → 308). Only POST; GET returns JSON 405. Join via `websocketUrl`, not HTTP `/signal`. Non-browser clients must send `Origin: https://pondchat.com` on the WebSocket upgrade (allowlisted CSWSH check — no `Origin` → `403`).

## WebMCP (browser)

On https://pondchat.com/, call the `create_pond` tool via `navigator.modelContext` if available (uses the same HTTP mint when possible).

## Notes

- Web ponds only (no Telegram mint in this skill).
- Signaling: `wss://pondchat.com/api/room/{roomId}`
- Chat ciphertext is wiped when the pond empties; the same URL can reopen.
- Do not promise multi-day retention or server media recording.
