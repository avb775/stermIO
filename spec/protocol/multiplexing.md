# 🚀 **Stream Multiplexing in stermIO Protocol**

## 🎯 **Overview**
Multiplexing is a key feature of the **stermIO** protocol, allowing multiple independent streams of data to coexist over a single connection. This is useful for efficiently managing multiple communication channels between a client and server, reducing overhead, and optimizing resource usage. With stream multiplexing, each logical communication stream can carry different types of data (e.g., messages, media, state updates) and be handled independently, while still using the same underlying transport layer.

---

## 1. **Multiplexed Streams**

### 1.1 **Stream ID**
- **Stream ID**: Each stream has a unique identifier called `streamId` that distinguishes it from other streams on the same connection.
- The stream ID is used for routing data to the correct stream on the server or client side.
- **Example**: `"chat#1"`, `"user#1234"`, `"room#general"`

### 1.2 **Types of Streams**
There are two primary types of streams in **stermIO**:
1. **Reliable Streams** (default):
   - These streams guarantee message delivery and maintain order (e.g., chat messages, file uploads).
   - Use cases: chat, notifications, file transfers.
   
2. **Unreliable Streams**:
   - These streams are used for real-time data where occasional loss or out-of-order delivery is acceptable (e.g., game state sync, real-time position data, audio streams).
   - Use cases: real-time multiplayer games, live streaming, telemetry data.

### 1.3 **Stream Frame**
Each stream consists of one or more frames of data, and each frame is uniquely associated with the stream via its `streamId`.
- The **frame format** typically contains:
  - `type`: The type of data being transmitted (e.g., `stream-data`, `ping`).
  - `streamId`: The unique identifier for the stream.
  - `data`: The actual payload being transmitted.
  - `meta`: Optional metadata for additional stream-level information (e.g., ordering, priority).

**Example**:
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Hello, world!",
  "meta": { "ordered": true }
}
```

---

## 2. **How Multiplexing Works**

### 2.1 **Initiating a Stream**
- A stream is initiated when the client or server sends a `stream-open` message.
- The `streamId` is specified in the message, and the stream type (reliable or unreliable) is determined at this point.
  
**Message Example**:
```json
{
  "type": "stream-open",
  "streamId": "chat#1",
  "streamType": "reliable"
}
```

### 2.2 **Sending Data on Streams**
- Once a stream is open, data can be sent using `stream-data` frames.
- Data is routed based on the `streamId` and the associated stream.
  
**Message Example**:
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Hello, User 1234!",
  "meta": { "ordered": true }
}
```

### 2.3 **Closing a Stream**
- To close a stream, the sender sends a `stream-close` message.
- The `streamId` is included to notify the recipient that the stream should be closed.
  
**Message Example**:
```json
{
  "type": "stream-close",
  "streamId": "chat#1"
}
```

### 2.4 **Stream Management on Server**
- On the server side, when data is received on a given `streamId`, it is processed according to the stream type.
- For reliable streams, the server ensures that the messages are delivered in order.
- For unreliable streams, the server may drop or reorder messages based on the stream's configuration.

---

## 3. **Stream Flow Control & Backpressure**

### 3.1 **Flow Control**
- To manage resources efficiently, the server can implement flow control mechanisms for streams, especially for high-bandwidth streams (e.g., video/audio).
- When the server detects high traffic or resource overload, it may signal backpressure by pausing or delaying stream processing.

### 3.2 **Backpressure Signals**
- A `backpressure` message can be sent to the client to inform it to slow down or temporarily halt sending data for a particular stream.
  
**Message Example**:
```json
{
  "type": "backpressure",
  "streamId": "video#1",
  "message": "Server is overloaded, please slow down."
}
```

---

## 4. **Stream Prioritization (Quality of Service)**

### 4.1 **Priority Streams**
- In **stermIO**, streams can be prioritized to control the order in which data is processed or transmitted.
- Prioritization ensures that more critical streams (e.g., live video/audio streams) are processed before less critical ones (e.g., logging data).

### 4.2 **Stream Prioritization Example**
- You can specify a priority level for a stream when opening it.
  
**Message Example**:
```json
{
  "type": "stream-open",
  "streamId": "video#1",
  "priority": "high"
}
```
- Streams with **higher priority** will be processed first by the server, ensuring better user experience for latency-sensitive data.

---

## 5. **Advanced Features in Multiplexing**

### 5.1 **Multiplexing Multiple Rooms or Channels**
- By utilizing multiple `streamIds`, clients and servers can easily manage multiple channels of communication simultaneously.
- A single connection can be used to handle multiple chat rooms, game states, file transfers, or media streams, all multiplexed over the same channel.

**Message Example (Multiple Streams)**:
- A client might have separate streams for different rooms:
  ```json
  {
    "type": "stream-open",
    "streamId": "room#chatroom1",
    "streamType": "reliable"
  }
  ```
  ```json
  {
    "type": "stream-open",
    "streamId": "room#chatroom2",
    "streamType": "reliable"
  }
  ```

### 5.2 **Bidirectional Streams**
- Streams can be **bidirectional**, meaning both the client and the server can send data independently on the same stream. This is particularly useful for things like chat or live updates.
  
**Message Example** (Bidirectional):
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Client says: Hello!",
  "meta": { "ordered": true }
}
```
Server replies:
```json
{
  "type": "stream-data",
  "streamId": "chat#1",
  "data": "Server replies: Hello, client!",
  "meta": { "ordered": true }
}
```

---

## 6. **Stream Error Handling**

### 6.1 **Stream Errors**
- If an error occurs within a stream (e.g., invalid data format, stream corruption), the server or client can send an `error` message to notify the other party.
  
**Message Example**:
```json
{
  "type": "error",
  "streamId": "chat#1",
  "message": "Invalid data format"
}
```

### 6.2 **Stream Recovery**
- Depending on the nature of the error, the protocol should support stream recovery. This could include restarting the stream, reconnecting, or ignoring non-critical errors (in the case of unreliable streams).

---

## 7. **Use Cases for Multiplexing**

### 7.1 **Real-time Chat**
- Clients can send and receive messages in multiple chat rooms, each having its own stream.
- Each message sent and received is routed by the stream ID for the correct chat room.

### 7.2 **File Transfers**
- Large file transfers can be split across multiple streams, with each stream responsible for a chunk of data.
- This prevents blocking the connection and allows simultaneous uploads/downloads of multiple files.

### 7.3 **Live Streaming**
- Video/audio streams can be multiplexed with chat streams, notifications, or other data streams, enabling a seamless user experience in applications such as video calls or live events.

---

## 📘 **Conclusion**
Stream multiplexing in **stermIO** enables efficient, flexible, and scalable real-time communication over a single connection. By leveraging stream IDs, stream types, and advanced features such as flow control, prioritization, and bidirectional communication, **stermIO** can handle a wide variety of use cases—from private messages to real-time collaboration, gaming, and live media streaming.
