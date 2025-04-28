# 🚀 **Message Types in stermIO Protocol**

## 🎯 **Overview**
Messages are the core units of communication in the **stermIO** protocol. They are used to transmit data, control information, and manage the state of streams and connections. These messages follow a structured format to ensure that both client and server can interpret and process them correctly. This document provides an overview of the different message types used in **stermIO**.

---

## 1. **Message Structure**

Each message consists of a **type** and various associated fields. Below is a basic message structure:

```json
{
  "type": "message-type",
  "streamId": "stream-identifier",
  "data": "payload-data",
  "meta": { "optional-metadata" }
}
```

- **type**: Specifies the type of the message (e.g., `stream-data`, `ping`, `stream-open`).
- **streamId**: The unique identifier for the stream this message belongs to.
- **data**: The actual data being sent in the message.
- **meta**: Optional metadata for additional context (e.g., ordering, priority, message size).

---

## 2. **Message Types**

### 2.1 **`ping` / `pong`**

- **Purpose**: Used for connection health checks and maintaining the connection alive.
- **Direction**: Bi-directional.
- **Details**:
  - `ping`: Sent by the client or server to check if the connection is still active.
  - `pong`: Sent in response to a `ping` message to confirm the connection is alive.

**Example** (`ping`):
```json
{
  "type": "ping",
  "streamId": "ping#1",
  "data": "timestamp"
}
```

**Example** (`pong`):
```json
{
  "type": "pong",
  "streamId": "ping#1",
  "data": "timestamp"
}
```

---

### 2.2 **`stream-open` / `stream-close`**

- **Purpose**: Used to open and close a stream between client and server.
- **Direction**: Client → Server (for opening); Server → Client (for closing).
- **Details**:
  - `stream-open`: Sent to open a new stream.
  - `stream-close`: Sent to close an existing stream.

**Example** (`stream-open`):
```json
{
  "type": "stream-open",
  "streamId": "chat#1",
  "streamType": "reliable"
}
```

**Example** (`stream-close`):
```json
{
  "type": "stream-close",
  "streamId": "chat#1"
}
```

---

### 2.3 **`stream-data`**

- **Purpose**: Carries the actual data payload within a stream.
- **Direction**: Client ↔ Server.
- **Details**:
  - Sent after a stream is opened.
  - Contains data to be transmitted to the other party.

**Example** (`stream-data`):
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Hello, world!",
  "meta": { "ordered": true }
}
```

---

### 2.4 **`error`**

- **Purpose**: Used to notify the recipient of an error that occurred during stream or connection handling.
- **Direction**: Client ↔ Server.
- **Details**:
  - Can be used to indicate an error in message format, stream state, or internal server/client issues.
  - Includes an error message and the `streamId` to identify which stream the error pertains to.

**Example** (`error`):
```json
{
  "type": "error",
  "streamId": "chat#1",
  "data": "Invalid data format",
  "meta": { "errorCode": 400 }
}
```

---

### 2.5 **`auth`**

- **Purpose**: Used for client authentication during the connection process.
- **Direction**: Client → Server.
- **Details**:
  - Contains authentication information, such as JWT or API keys, sent by the client.
  - Ensures that the server can verify the client’s identity and grant or deny access.

**Example** (`auth`):
```json
{
  "type": "auth",
  "streamId": "auth#1",
  "data": "JWT-token",
  "meta": { "userId": "1234", "roles": ["admin", "user"] }
}
```

---

### 2.6 **`resume`**

- **Purpose**: Used to resume a previous session after a disconnect.
- **Direction**: Client → Server.
- **Details**:
  - Sent when a client wants to reconnect and resume the last active session.
  - Includes the session token or session ID to identify the session being resumed.

**Example** (`resume`):
```json
{
  "type": "resume",
  "streamId": "session#1234",
  "data": "session-token"
}
```

---

### 2.7 **`presence`**

- **Purpose**: Used to manage presence information within a stream or room.
- **Direction**: Client ↔ Server.
- **Details**:
  - Indicates the presence or absence of a user in a specific stream or room.
  - Can also include metadata such as status, role, or activity.

**Example** (`presence`):
```json
{
  "type": "presence",
  "streamId": "chatroom#1",
  "data": "user-1234",
  "meta": { "status": "active", "activity": "typing" }
}
```

---

## 3. **Message Metadata (Optional)**

In addition to the core message fields (`type`, `streamId`, `data`), messages can include optional metadata for additional functionality:

- **ordered**: Indicates whether the data should be delivered in order.
- **priority**: Used to prioritize streams (e.g., high/low priority).
- **errorCode**: Includes an error code when an error message is sent.
- **sessionId**: Identifies the session associated with the message.
- **status**: Defines the status of the user or stream (e.g., active, idle, disconnected).

---

## 4. **Use Cases for Messages**

### 4.1 **Real-Time Chat**
- The `stream-open`, `stream-data`, and `presence` messages enable real-time chat between users in specific rooms or channels.
- Users can join a stream, send and receive messages, and indicate their presence.

### 4.2 **File Transfers**
- File transfer can be handled with multiple `stream-data` messages, each containing chunks of a file to be uploaded or downloaded.

### 4.3 **Game State Sync**
- The `stream-data` message type can be used to sync game state between clients, while `presence` messages track player status.

### 4.4 **Authentication & Session Management**
- The `auth` message ensures that only authenticated users can access streams, while the `resume` message allows users to continue sessions after a disconnect.

---

## 📘 **Conclusion**
The **stermIO** protocol defines a rich set of message types to handle various use cases, from basic data transmission to real-time chat, file transfers, and more. The ability to use structured message types, paired with optional metadata, makes **stermIO** flexible and extensible for diverse real-time applications.
