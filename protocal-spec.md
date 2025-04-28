# 🚀 **stermIO Protocol Specification (v0.1)**

## 🎯 **Overview**
**stermIO** is a scalable, transport-agnostic protocol designed for real-time communication across both serverless and serverful platforms. It is optimized for high-performance applications, offering features like multiplexed streams, low-latency communication, and cross-platform support. **stermIO** is ideal for scenarios such as chat, gaming, media streaming, and more.

This document outlines the core specification for **stermIO**, including connection lifecycle, stream multiplexing, message types, authentication, and transport details.

---

## 🔗 **Connection Lifecycle**

The connection lifecycle follows a set of stages to establish, maintain, and gracefully close a communication session between a client and server.

1. **CONNECT**: 
   - **Client** initiates the connection, sending an authentication token.
   - **Server** validates the token and returns an acknowledgment.
   
   Example:
   ```json
   {
     "type": "connect",
     "authToken": "JWT_OR_API_KEY"
   }
   ```

2. **ACCEPT**: 
   - The server responds with a session ID to the client, confirming the connection.
   
   Example:
   ```json
   {
     "type": "accept",
     "sessionId": "SESSION_ID"
   }
   ```

3. **RESUME**: 
   - If the connection is lost, the client can attempt to resume by sending a session token to the server.
   
   Example:
   ```json
   {
     "type": "resume",
     "sessionToken": "SESSION_TOKEN"
   }
   ```

4. **CLOSE**: 
   - The connection is gracefully closed, either by the client or server.
   
   Example:
   ```json
   {
     "type": "close",
     "sessionId": "SESSION_ID"
   }
   ```

---

## 🔄 **Stream Multiplexing**

Each logical communication channel is represented by a **stream**, which is multiplexed over the connection. Streams are identified by unique **stream IDs**.

### Types of Streams:
- **Reliable**: Default stream type, ensures data delivery and ordering.
- **Unreliable**: For real-time data like position updates or audio where packet loss is acceptable.

### Stream Frame:
A stream frame consists of the data being transmitted and any associated metadata. It is transmitted within a **stream packet**.

Example:
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "...",
  "meta": {
    "ordered": true
  }
}
```

---

## 🧱 **Message Types**

The protocol defines several message types to control the flow of data and manage the connection.

- **ping / pong**: Used for health checks and to maintain the connection.
- **stream-open / stream-close**: Opens or closes a stream.
- **stream-data**: Carries data payload for a specific stream.
- **error**: Represents an error message.
- **auth**: Sends authentication-related information.
- **resume**: Requests to resume a previously interrupted session.
- **presence**: For tracking presence information (who’s online, etc.).

---

## 🔐 **Auth & Identity**

The protocol supports **authentication** via tokens, including **JWT** (JSON Web Tokens) or **API keys**.

- **Authentication** occurs during the **CONNECT** phase.
- **Authorization** (per-stream access control) is enforced by the server.
- **Identity Claims** include:
  - `userId`: Unique identifier for the user.
  - `deviceId`: Device identifier (useful for multi-device environments).
  - `roles`: List of roles assigned to the user (e.g., admin, user).

---

## 📁 **Payload Format**

- The default payload format is **JSON**.
- Future versions will support **binary formats** such as **MessagePack** and **Protocol Buffers** for reduced payload size and faster transmission.

Example JSON frame:
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Hello, world!",
  "meta": {
    "ordered": true
  }
}
```

---

## 📦 **Transport Support**

**stermIO** is designed to be transport-agnostic, supporting a variety of transport protocols:

- **WebSocket (v1)**: The initial implementation, suitable for browsers and many server environments.
- **TCP (native)**: A direct TCP implementation for more control and reliability.
- **WebTransport (planned)**: A next-generation protocol for secure, low-latency communication.
- **QUIC (future)**: High-performance transport protocol for low-latency connections.

---

## 🛣 **Use Cases**

**stermIO** is highly versatile and can be applied to various real-time communication scenarios, including:

- **Chat & Presence**: Real-time messaging and presence tracking (who's online).
- **Game State Sync**: Multiplayer game synchronization with low-latency.
- **Real-time Dashboards**: Live updates for financial, IoT, and monitoring dashboards.
- **Media Signaling**: WebRTC-like signaling for video/audio communication.
- **File Upload/Download**: Efficient data streaming, especially for large files.

---

## 📈 **Future Extensions (Planned)**

### 🔄 **Reconnection & Resume**
- Full support for session re-connection and resuming without data loss.
  
### 🔁 **Stream Backpressure**
- Allow the server to signal flow control to clients to avoid overwhelming them with data.

### 🧠 **Presence & Awareness API**
- Broadcast the presence state and allow clients to be aware of others' activities (e.g., cursor positions, editing status).

### 🔐 **Fine-Grained ACL**
- Implement stream-based permissions for more granular access control.

### 🎧 **Media & Binary Stream Support**
- Chunked streaming for media files with codec and type metadata.
  
### 🌍 **Edge Routing & Geo-Awareness**
- Smart routing for better latency and fault tolerance based on geographic location.

---

## 📘 **License & Project Info**

- **License:** MIT
- **Open Source:** Yes
- **Hosted:** GitHub (coming soon)
- **Maintainers:** @Avijitbera

