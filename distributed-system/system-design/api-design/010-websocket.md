## WebSocket

### 🔍 **Motivation**

Most web APIs use **HTTP**, which is suitable for *request-response* and *batch* operations. However, HTTP has limitations in **real-time, bidirectional communication** (e.g., chat apps, gaming, live streaming).

| Technique          | Description                                               | Limitation                                                |
| ------------------ | --------------------------------------------------------- | --------------------------------------------------------- |
| **Short Polling**  | Client sends frequent periodic requests for updates       | High overhead due to unnecessary repeated requests        |
| **Long Polling**   | Client holds the request open until the server responds   | Resource-intensive due to multiple concurrent connections |
| **HTTP Streaming** | Server streams data continuously over a single connection | Limited by half-duplex nature (client/server take turns)  |

> 🧠 **Need**: Efficient **full-duplex** communication without repeated overhead → **WebSocket**

---

### 🔧 **What is WebSocket?**

* **WebSocket** is a **stateful, full-duplex** communication protocol ideal for real-time applications.
* **Defined in**: RFC 6455 (IETF), API by W3C.
* **Starts with HTTP**, then **upgrades** to WebSocket.
* Communication happens directly over a **persistent TCP connection**.

**Connection URL Prefixes**:

* `ws://` — Non-secure WebSocket
* `wss://` — Secure WebSocket (over TLS)

---

### 🔄 **How Does WebSocket Work?**

1. **Client initiates HTTP GET request** with headers to upgrade the connection:

   ```
   GET / HTTP/1.1
   Connection: Upgrade
   Upgrade: websocket
   Sec-WebSocket-Key: <base64>
   Sec-WebSocket-Version: 13
   ```

2. **Server responds** with a **101 Switching Protocols** response:

   ```
   HTTP/1.1 101 Switching Protocols
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Accept: <hash>
   ```

3. **Protocol switches**, and the TCP connection stays open for bi-directional data exchange.

---

### 📦 **WebSocket Frame Types**

WebSocket transmits messages in **frames**. Two categories:

| Frame Type | Opcode | Description                                  |
| ---------- | ------ | -------------------------------------------- |
| **Text**   | `0x1`  | Carries plain text messages                  |
| **Binary** | `0x2`  | Carries binary data                          |
| **Ping**   | `0x8`  | Server checks if the client is alive         |
| **Pong**   | `0x9`  | Client replies to a ping                     |
| **Close**  | `0xA`  | Either party signals to close the connection |

* **Control frames** (e.g., ping/pong/close): Max 125 bytes
* **Data frames**: Carry application payloads (text/binary)

---

### ✅ **Advantages of WebSocket**

* **Bidirectional communication**: Both client and server can send data anytime.
* **Low latency**: Minimal overhead (2–10 bytes/frame).
* **Real-time support**: Ideal for high-frequency updates.
* **Firewall-friendly**: Uses standard ports `80` and `443`.

---

### ⚠️ **Limitations of WebSocket**

* **Stateful connection**: Makes **horizontal scaling** more difficult (e.g., sticky sessions/load balancing challenges).
* **Connection reliability**: Harder to recover from connection failures due to lack of context in headers.
* **Less mature**: Compared to well-established HTTP REST patterns.

---

### 🎯 **When to Use WebSocket**

Use WebSocket when:

* Low-latency, real-time updates are needed (e.g., multiplayer games, stock tickers, collaborative tools).
* You need **full-duplex** communication.

Avoid WebSocket when:

* Your app is CRUD-based or doesn't need real-time capabilities.
* You rely heavily on stateless, cacheable HTTP-based infrastructure.
