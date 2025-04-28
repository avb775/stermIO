# WebTransport Transport

## 📦 Overview

**WebTransport** is a modern transport protocol that runs over **HTTP/3 and QUIC**, designed for **low-latency**, **secure**, and **bidirectional** communication — perfect for browsers, mobile apps, and serverless/edge platforms.

It’s a powerful next-generation alternative to WebSocket.

---

## 🌐 Why WebTransport?

- **Runs over QUIC**: Faster than TCP + TLS + HTTP/1.1 or HTTP/2.
- **Multiplexed Streams**: Native support for multiple independent streams.
- **Unidirectional and Bidirectional Streams**: Flexible stream types per use case.
- **Built-in Security**: Always encrypted (TLS 1.3).
- **Browser and Edge Support**: Works in Chrome, Safari, and serverless platforms like Cloudflare Workers, Vercel, AWS Lambda@Edge (with limitations).

---

## ⚡ How stermIO Uses WebTransport

- Client establishes a WebTransport session with the server.
- Each logical stermIO stream (chat, audio, file upload, etc.) maps to a WebTransport stream.
- Binary framing used to encapsulate stermIO protocol messages.
- Supports true parallel, independent data streams.
- Supports both *reliable* (ordered) and *unreliable* (unordered) delivery options.

---

## 📜 Frame Usage in WebTransport

- **Control Stream**: For `CONNECT`, `ACCEPT`, `PING`, `PONG`, and session control messages.
- **Data Streams**: For `stream-data`, `stream-open`, and `stream-close` messages.
- **Encoding**: Binary by default (with MessagePack or Protobuf planned).

---

## 🔐 Security

- **Always Secured**: WebTransport mandates TLS 1.3 encryption.
- **Authentication**:
  - JWT or API key passed in the `CONNECT` frame.
  - Session resumption supported via token-based reconnects.
- **Access Control**: Server enforces stream-level ACLs and authorization.

---

## 🛠 Server Deployment Considerations

- **HTTP/3 Gateway Required**: Server must support QUIC and HTTP/3 (e.g., Envoy Proxy, Cloudflare, Caddy).
- **KeepAlive**: Use QUIC keepalives or periodic pings to detect idle connections.
- **Scaling**:
  - Sticky sessions are preferred for stateful connections.
  - Distributed brokers (Redis, NATS, Kafka) help scale to multiple nodes.
- **Error Handling**: Graceful degradation if client doesn't support WebTransport (fallback to WebSocket).

---

## 🚀 Example Flow

```plaintext
Client -> Server : Establish WebTransport Session
Client -> Server : [CONNECT frame]
Server -> Client : [ACCEPT frame]
Client -> Server : Open New Stream for Chat
Client -> Server : [stream-open frame]
Client -> Server : [stream-data frame]
Server -> Client : [stream-data frame]
...
Client -> Server : Close Stream
Client -> Server : [stream-close frame]
```

---

## 📈 Benefits for Serverless & Edge

- **Serverless Friendly**: Designed for stateless, short-lived deployments.
- **Edge Native**: Low latency with HTTP/3/QUIC edge nodes.
- **No need for TCP KeepAlive Hacks**: QUIC handles it natively.
- **Automatic Multiplexing**: Handles thousands of lightweight streams in one session.

---

## 🧠 Future Enhancements

- Connection migration (seamless client IP change without reconnect).
- Explicit priority settings for streams (e.g., video > chat > logs).
- Native unreliable datagrams for real-time gaming use cases.
