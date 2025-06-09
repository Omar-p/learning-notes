# Video Transmission Protocols and Real-Time Conferencing

## Video Transmission Protocols: Adaptive Streaming

Two key adaptive video transmission protocols are **DASH** and **HLS**. Both aim to provide smooth video playback by adjusting quality based on network conditions.

### Dynamic Adaptive Streaming over HTTP (DASH)

- **Core Concept:**  
  DASH is an international standard that delivers video via HTTP. It splits videos into small segments at different quality levels, allowing the client to choose the best one based on current bandwidth.

- **Uses TCP:**  
  DASH relies on TCP for reliable and ordered delivery of video segments.

- **How it Works:**
  1. Video is encoded at various quality levels (e.g., 240p, 480p, 720p, 1080p).
  2. Each quality level is divided into small chunks (2–10 seconds).
  3. A manifest file (MPD - Media Presentation Description) describes all segments.
  4. The client downloads the MPD file.
  5. During playback, the client monitors bandwidth and buffer levels.
  6. It requests segments dynamically, switching quality based on conditions.

- **Advantages:**
  - Adapts to varying network speeds.
  - Open standard – future-proof and widely supported.
  - CDN-friendly: segments can be cached easily.

### HTTP Live Streaming (HLS)

- **Core Concept:**  
  Developed by Apple, HLS is another adaptive streaming protocol using a different manifest format.

- **Uses TCP:**  
  Ensures reliable delivery of media data.

- **How it Works:**
  1. Video is encoded at multiple bitrates and segmented into `.ts` files.
  2. A master `.m3u8` playlist lists variant playlists for each bitrate.
  3. Each variant playlist includes its `.ts` segments.
  4. The client selects a suitable variant based on bandwidth.
  5. It downloads and plays segments, switching quality as needed.

- **Advantages:**
  - Broad support, especially on Apple devices.
  - Simple implementation.
  - CDN-friendly: segments are easily cached.

---

## Real-Time Video Conferencing: WebRTC

### WebRTC (Web Real-Time Communication)

- **Core Concept:**  
  WebRTC is an open-source standard for real-time peer-to-peer communication (video, audio, and data) directly between browsers.

- **Browser-Based:**  
  Works entirely in the browser via JavaScript APIs.

- **Peer-to-Peer (P2P):**  
  Avoids routing media through central servers, reducing latency and server costs.

### Signaling

- **Purpose:**  
  Establishes the initial connection between peers (not part of WebRTC itself).

- **How it Works:**
  - A signaling server (e.g., WebSocket or HTTP server) exchanges connection metadata.
  - Peers exchange **SDP (Session Description Protocol)** messages that include:
    - Supported codecs (VP8, H.264, Opus, etc.)
    - IP addresses and ports
    - Encryption keys

### NAT and Firewall Challenges

- **Problem:**  
  NATs/firewalls often obscure a peer’s actual IP, complicating direct connections.

- **ICE (Interactive Connectivity Establishment):**  
  A framework to find the best possible route between peers.

  - **Candidate Types:**
    - **Host:** Local IP address.
    - **Server Reflexive:** Public IP from STUN.
    - **Relay:** IP from TURN server.

- **STUN (Session Traversal Utilities for NAT):**
  - Helps discover public IP address.
  - Works for many NAT types.
  - **Limitation:** Fails with symmetric NATs.

- **TURN (Traversal Using Relays around NAT):**
  - Used when STUN fails.
  - Relays media through a public server.
  - **Disadvantage:** Higher latency and server cost.

### WebRTC Flow Summary

1. **Signaling:** Exchange of SDP offers/answers via a signaling server.
2. **Candidate Gathering:** Peers collect ICE candidates.
3. **Candidate Exchange:** Exchange of ICE candidates through signaling.
4. **Connectivity Checks:** Attempt direct or relayed connection.
5. **Media Exchange:** Direct P2P media streaming (or via TURN if necessary).

---

## Key Takeaways

| Protocol | Purpose                    | Uses TCP | Adaptive | Real-Time | Peer-to-Peer | Requires Server |
|----------|-----------------------------|---------|----------|-----------|---------------|------------------|
| DASH     | Streaming video over HTTP   | ✅      | ✅       | ❌        | ❌            | ✅ (CDN, HTTP)   |
| HLS      | Apple’s streaming protocol  | ✅      | ✅       | ❌        | ❌            | ✅ (CDN, HTTP)   |
| WebRTC   | Real-time communication     | ✅/❌\* | ❌       | ✅        | ✅            | ✅ (for signaling) |

> \* WebRTC uses UDP primarily for real-time performance but may fall back to TCP or TURN relays depending on network conditions.
- https://fireship.io/lessons/webrtc-firebase-video-chat/