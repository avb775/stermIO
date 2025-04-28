# 📡 stermIO Protocol Specification (v0.1)

---

## 🎯 Overview

**stermIO** is a transport-agnostic, scalable real-time communication protocol that enables reliable and unreliable streams over WebSocket, TCP, WebTransport, and future transports like QUIC.  
It is built to work seamlessly in serverless, edge, and traditional server environments.

---

## 🔗 Connection Lifecycle

1. **CONNECT**  
   Client initiates connection with authentication token and optional metadata.

2. **ACCEPT**  
   Server accepts the connection and returns a session ID.

3. **RESUME**  
   Client can resume a previous session if disconnected unexpectedly, using a session token.

4. **CLOSE**  
   Either side can gracefully close the connection.

---

## 🔄 Stream Multiplexing

- A **single physical connection** can carry **multiple logical streams**.
- Each stream has a **`streamId`** to distinguish it.
- Streams can be:
  - `reliable` (e.g., chat, commands)
  - `unreliable` (e.g., live audio, cursor movements)

### Stream Frame Example

```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Hello World!",
  "meta": { "ordered": true }
}
```

---

## 🧱 Core Message Types

| Message Type | Purpose |
|:-------------|:--------|
| `ping`        | Heartbeat from client to server |
| `pong`        | Heartbeat response from server |
| `auth`        | Authentication payload |
| `stream-open` | Start a new logical stream |
| `stream-data` | Data message on a specific stream |
| `stream-close`| Close a specific stream |
| `error`       | Error signaling |
| `presence`    | User presence and awareness signaling |
| `resume`      | Request to resume a previous session |

---

## 🔐 Authentication & Identity

- Authentication must happen during `CONNECT`.
- Supported mechanisms:
  - JWT tokens
  - API keys
- Standard claims passed:
  - `userId`
  - `deviceId`
  - `roles`
- Per-stream ACLs (Access Control Lists) can restrict or permit actions.

---

## 📦 Payload Format

- Default encoding: **JSON**
- Future planned encodings:
  - **MessagePack** (binary efficient)
  - **Protobuf** (strong schema)

---

## 📡 Supported Transports

- **WebSocket** (✅ base version)
- **TCP** (🔜 native support)
- **WebTransport** (🔜 experimental)
- **QUIC** (🔜 future support)

---

## 🛣 Supported Use Cases

- Real-time chat and presence systems
- Multiplayer game state sync
- Live dashboards and event streams
- Media signaling (WebRTC offer/answer exchange)
- File upload/download streams (with chunking)

---

## 📈 Future Extensions

| Feature | Status |
|:--------|:-------|
| Reconnection & Session Resume | Planned |
| Backpressure and Flow Control | Planned |
| Presence & Awareness Metadata | Planned |
| Fine-Grained Stream ACLs | Planned |
| Media and Binary Streaming | Planned |
| Built-in Metrics (heartbeats, latency) | Planned |
| Edge Routing & Geo-awareness | Planned |
| Broker Integration (Redis, NATS, Kafka) | Planned |

---

## 🧠 Design Philosophy

- Minimal at the core, powerful with extensions.
- Transport-flexible: WebSocket, TCP, WebTransport.
- Works equally on serverless (e.g., AWS Lambda, Cloudflare Workers) and traditional servers.
- Optimized for real-world needs: reconnections, stream multiplexing, media handling, and global distribution.

---

