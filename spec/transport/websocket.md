# WebSocket Transport

## 📦 Overview

WebSocket is the **primary and default** transport for stermIO.  
It allows full-duplex, low-latency communication over a single TCP connection, making it ideal for real-time messaging, presence, and streaming.

---

## 🌐 Why WebSocket?

- **Widely Supported**: Works across all modern browsers and backend servers.
- **Bi-directional**: Enables both server-to-client and client-to-server communication.
- **Connection Persistence**: Unlike HTTP, WebSocket keeps the connection open.
- **Efficiency**: Minimal overhead once the handshake is complete.
- **Edge and Serverless Friendly**: Easily adaptable to serverless platforms with edge support (e.g., Cloudflare Workers, AWS Lambda with ALB).

---

## ⚡ How stermIO Uses WebSocket

- Client initiates a WebSocket connection to the server.
- After handshake, stermIO frames and streams are multiplexed over the WebSocket.
- Each logical stream has a `streamId`.
- Supports binary and JSON payloads.
- Reconnection and session resumption are possible over new WebSocket connections.

---

## 📜 WebSocket Frame Usage

- **Message Format**: All stermIO messages are serialized (initially JSON, later optimized with MessagePack or Protobuf).
- **Frame Types**:
  - `CONNECT`, `ACCEPT`, `RESUME`, `CLOSE`
  - `stream-open`, `stream-data`, `stream-close`
  - `ping`, `pong`
  - `auth`, `error`, `presence`

---

## 🔐 Security

- All WebSocket connections **must** be secured using **WSS** (WebSocket Secure over TLS).
- Clients must present a valid **auth token** during the `CONNECT` phase.
- Optional server-side validation of client IP, User-Agent, device ID for extra security.

---

## 🛠 Server Deployment Considerations

- **KeepAlive/Ping-Pong**: Server should periodically send `ping` frames to detect dead connections.
- **Idle Timeout**: Auto-close idle WebSocket connections to free resources.
- **Backpressure Management**: Handle slow clients to avoid memory bloat.
- **Horizontal Scaling**:
  - Use sticky sessions (based on cookie/session ID) behind Load Balancers.
  - External pub-sub (e.g., Redis, NATS, Kafka) recommended for multi-node event distribution.

---

## 🚀 Example Flow

```plaintext
Client -> Server : WebSocket handshake
Client -> Server : CONNECT { authToken }
Server -> Client : ACCEPT { sessionId }
Client -> Server : stream-open { streamId: "chat#123" }
Client -> Server : stream-data { streamId: "chat#123", data: "Hello" }
Server -> Client : stream-data { streamId: "chat#123", data: "World" }
...
Client -> Server : CLOSE
```

---

## 📈 Benefits for Serverless Platforms

- Works natively with WebSocket-enabled serverless backends (AWS API Gateway, Cloudflare Workers, Vercel, etc.).
- Auto-scale without worrying about managing TCP connections manually.
- Easier reconnection and stateless session resumption.

---

## 🧠 Future Improvements

- Support WebSocket subprotocol negotiation (`Sec-WebSocket-Protocol`).
- Dynamic switching between JSON/Binary encoding.
- Adaptive heartbeat intervals based on network quality.

