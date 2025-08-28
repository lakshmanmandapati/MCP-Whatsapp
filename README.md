# WhatsApp MCP Server

A **Model Context Protocol (MCP) server** for WhatsApp.  

This integration lets you:  
- Search and read your WhatsApp messages (text, images, videos, documents, audio).  
- Search contacts and send messages (individuals or groups).  
- Send media files (images, videos, documents, audio).  

It connects directly to your WhatsApp account using the WhatsApp Web Multi-Device API (via [whatsmeow](https://github.com/tulir/whatsmeow)).  
All messages are stored locally in SQLite. Data is only shared with an LLM (like Claude) when you explicitly use tools that request it.  

---

## Features
- Two-way communication with WhatsApp via MCP.  
- Local message storage (SQLite).  
- Full media support: images, videos, documents, and audio.  
- Optional FFmpeg integration for automatic audio conversion.  

---

## Requirements
- [Go](https://go.dev/doc/install) (>=1.20)  
- Python 3.6+  
- [UV](https://docs.astral.sh/uv/getting-started/installation/) (Python package manager)  
- Claude Desktop app **or** Cursor IDE  
- FFmpeg (optional, only required for voice message conversion)  

---

## Installation

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/whatsapp-mcp.git
cd whatsapp-mcp
```

### 2. Run the WhatsApp Bridge
```bash
cd whatsapp-bridge
go run main.go
```

- The first time, you’ll be prompted with a **QR code**.  
- Scan it using your WhatsApp app (`Settings > Linked Devices > Link a Device`).  
- Authentication may expire after ~20 days; simply re-scan when prompted.  

### 3. Run the MCP Server
In a new terminal:
```bash
cd whatsapp-mcp-server
uv run main.py
```

---

## Configure MCP

Add this configuration to your MCP client:

```json
{
  "mcpServers": {
    "whatsapp": {
      "command": "{{PATH_TO_UV}}",
      "args": [
        "--directory",
        "{{PATH_TO_SRC}}/whatsapp-mcp/whatsapp-mcp-server",
        "run",
        "main.py"
      ]
    }
  }
}
```

- **Claude Desktop**: Save as `claude_desktop_config.json` in  
  `~/Library/Application Support/Claude/claude_desktop_config.json`  
- **Cursor**: Save as `mcp.json` in  
  `~/.cursor/mcp.json`  

Restart Claude Desktop or Cursor. You should now see **WhatsApp** as an available integration.  

---

## Windows Notes
`go-sqlite3` requires **CGO** enabled.  

1. Install [MSYS2](https://www.msys2.org/) and ensure `ucrt64\bin` is in PATH.  
2. Enable CGO and run:  
   ```powershell
   cd whatsapp-bridge
   go env -w CGO_ENABLED=1
   go run main.go
   ```

---

## Data Storage
- SQLite DB stored at `whatsapp-bridge/store/`  
- Tables for chats and messages  
- Media metadata stored, full files can be downloaded with tools  

---

## Tools Available
- `search_contacts` – Search by name or number  
- `list_messages` – Retrieve messages with filters  
- `list_chats` – List all chats  
- `send_message` – Send text to contacts or groups  
- `send_file` – Send images, videos, documents, or audio  
- `send_audio_message` – Send playable WhatsApp voice messages (`.ogg opus`)  
- `download_media` – Download WhatsApp media locally  

---

## Media Support
- **Send**: images, videos, docs, audio  
- **Receive**: same as above  
- With FFmpeg installed, audio files are auto-converted to `.ogg Opus`.  
- Without FFmpeg, raw audio is still sent but not as WhatsApp voice notes.  

---

## Troubleshooting
- **No QR Code**: Restart `go run main.go` in `whatsapp-bridge`.  
- **Session expired**: Re-scan QR in your phone’s WhatsApp app.  
- **Device limit reached**: Unlink a device in `WhatsApp > Settings > Linked Devices`.  
- **Windows build errors**: Ensure CGO is enabled and a C compiler is installed.  
- **Out of sync messages**: Delete `messages.db` and `whatsapp.db` in `whatsapp-bridge/store/` and re-authenticate.  

---

