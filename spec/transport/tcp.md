# TCP Transport

## 📦 Overview

TCP is a **native transport** option for stermIO designed for environments where low-level socket control and maximum performance are needed — especially in server-to-server communication, mobile apps, games, and custom clients.

---

## 🌐 Why TCP?

- **Minimal Overhead**: No HTTP or WebSocket framing overhead.
- **Full Control**: Fine-grained control over connection behavior, buffering, and congestion.
- **High Performance**: Ideal for high-throughput, low-latency real-time applications.
- **Custom Framing**: stermIO defines its own lightweight frame format on top of TCP streams.

---

## ⚡ How stermIO Uses TCP

- TCP connections are **stateful**.
- A lightweight framing protocol wraps stermIO messages (length-prefix + message body).
- Streams are multiplexed over a single TCP connection, like WebSocket.
- Supports both **reliable** and **unreliable** message delivery (with best-effort support).
- Connection resumption can be handled by reconnecting and resending session tokens.

---

## 📜 TCP Frame Format

Each message over TCP has a custom binary frame:

```
+-----------------+-----------------+----------------+
|  Frame Length   |  Frame Type      |   Frame Payload |
|  (4 bytes)      |  (1 byte)        |   (variable)    |
+-----------------+-----------------+----------------+
```

- **Frame Length**: 32-bit unsigned integer (big-endian).
- **Frame Type**: 8-bit integer representing message type (e.g., CONNECT, STREAM-DATA).
- **Frame Payload**: Binary or JSON-encoded payload.

---

## 🔐 Security

- TCP connections **must** be secured with **TLS** when used over public networks.
- Optionally, use mTLS (mutual TLS) for client certificate authentication.
- Application-layer auth (`CONNECT` message with token) is still required.

---

## 🛠 Server Deployment Considerations

- **KeepAlive Settings**: Enable TCP KeepAlive to detect dead peers.
- **Backpressure**: Implement internal flow control to avoid buffer overflows.
- **Load Balancing**:
  - Use Layer 4 load balancers (e.g., NLB, HAProxy, Envoy with TCP Proxy mode).
  - Sticky sessions recommended if session state is server-bound.
- **Connection Limits**: Set reasonable per-IP and global connection limits.

---

## 🚀 Example Flow

```plaintext
TCP Connection Open
Client -> Server : [CONNECT frame]
Server -> Client : [ACCEPT frame]
Client -> Server : [stream-open frame]
Client -> Server : [stream-data frame]
Server -> Client : [stream-data frame]
...
Client -> Server : [CLOSE frame]
TCP Connection Close
```

---

## 📈 Benefits for Serverful and Serverless

- In **serverful environments** (bare-metal, VMs, Kubernetes), TCP offers maximum control and performance.
- In **serverless platforms**, TCP is harder but possible via specialized providers (e.g., AWS App Runner, Cloudflare TCP Tunnels).

*Note*: TCP on serverless is limited; WebSocket or WebTransport is preferred for compatibility.

---

## 🧠 Future Enhancements

- Framing extensions (optional compression, message priority).
- TCP Fast Open support for even faster connection times.
- Adaptive flow control based on network conditions.
