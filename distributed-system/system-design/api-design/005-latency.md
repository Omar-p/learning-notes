## 🔁 **Latency vs Throughput**

### ✅ **Latency**

* **Definition**: Time taken for a message to travel from sender to receiver (usually measured as Round-Trip Time, RTT).
* **User-perceived latency** includes:

  * **Transmission delay**: Time to push packet onto the wire.

    * `Transmission Delay = Packet Size / Bandwidth`
  * **Propagation delay**: Time a bit takes to travel from sender to receiver.

    * `Propagation Delay = Distance / Propagation Speed`
  * **Queuing delay**: Waiting time at routers.
  * **Processing delay**: Time to process the packet at each node.
* **Jitter**: Variation in latency between packets. High jitter causes buffering (e.g., in video playback).

### ✅ **Throughput**

* **Definition**: Actual rate of successful message delivery over a communication channel.
* **Measured in**: bits per second (bps), e.g., 1 Mbps.
* **Example**: If a network supports 2 Mbps but an IoT device processes only 1 Mbps, effective throughput is limited to 1 Mbps.

---

## 🧮 **Latency-Bandwidth Product**

* **Formula**:

  ```
  Latency × Bandwidth
  ```

* **Meaning**: Maximum number of bits “in flight” at any time.

* **Example**:
  If `Bandwidth = 1 Mbps`, `Latency = 200 ms`:

  ```
  200 × 10⁻³ sec × 1 × 10⁶ bps = 200,000 bits = 200 kb
  ```

  → This is the amount of unacknowledged data that can be in the network at once.

* **RTT × Bandwidth** gives the data a sender can transmit before needing an acknowledgment (especially relevant for TCP).

---

## 🧩 **Impacts of Network Elements**

* **Cross-continental API calls**:
  \~150–250 ms of the 500 ms budget consumed by network delay alone.
* **DNS resolution**:
  May introduce additional delay if not cached.
* **Multiple TCP connections**:
  Increase latency due to repeated connection establishment (handshakes).

---

## 🧠 **Application Classifications**

### 🔹 **Elastic Applications** (Tolerant to delay)

| Type             | Description                                   | Example           |
| ---------------- | --------------------------------------------- | ----------------- |
| Interactive      | Needs user input + fast response              | Telnet, CLI tools |
| Interactive Bulk | Transfers large data with some responsiveness | FTP               |
| Asynchronous     | Operates independently of responses           | Email             |

### 🔹 **Real-Time Applications** (Delay-sensitive)

| Type            | Tolerance to Delay        | Example                         |
| --------------- | ------------------------- | ------------------------------- |
| Hard/Intolerant | No tolerance              | Live video conferencing         |
| Soft/Tolerant   | Some tolerance            | Web browsing                    |
| Adaptive        | Adjusts to network delay  | Streaming with dynamic playback |
| Non-Adaptive    | Fixed behavior regardless | Static-delay playback systems   |

---

## 🎯 **Design Takeaways**

* **High-latency networks** (like global APIs) require optimization strategies:

  * **Minimize round-trips** (e.g., batch API calls).
  * **Use connection reuse** (e.g., HTTP keep-alive, HTTP/2 multiplexing).
  * **DNS caching** and **connection pooling** improve performance.
* **Understanding latency and throughput tradeoffs** helps in:

  * API design
  * Protocol choice
  * User experience optimization

