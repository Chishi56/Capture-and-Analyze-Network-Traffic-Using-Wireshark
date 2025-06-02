# Capture-and-Analyze-Network-Traffic-Using-Wireshark
## Objective
To capture live network traffic using Wireshark and analyze different protocols like DNS, TCP, and TLS.

---

# Environment and Tools Used
## Environment
- Windows 11
## Tools
- Wireshark
- Web Browser

## Captured Protocols
### 1. DNS
- DNS Query Details <br>


| **No.** | **Domain Queried**      | **Record Type** | **CNAME Chain**                                     | **Resolved IP**                    |
| ------- | ----------------------- | --------------- | --------------------------------------------------- | ---------------------------------- |
| 83      | `www.bing.com`          | AAAA            | `trafficmanager.net → edgekey.net → akamaiedge.net` | `2600:140f:6600::17d3:8999`        |
| 107      | `www.flipkart.com`      | AAAA            | `static-assets-web.flipkart.com → akamaiedge.net`   | `23.211.137.152`, `23.211.137.162` |
| 108    | `rukunim2.flipkart.com` | A, AAAA         | `rukunim2.edgekey.net → akamaiedge.net`             | `23.57.76.145`, `23.57.76.146`     |

  
### Observation <br> 
During DNS analysis, several queries were observed for domains like www.bing.com and www.flipkart.com. These queries used both A (IPv4) and AAAA (IPv6) records. The domain www.bing.com followed a CNAME redirection chain from trafficmanager.net to edgekey.net, eventually resolving through Akamai CDN (akamaiedge.net). This resolved to both an IPv4 address (23.212.164.211) and an IPv6 address (2600:140f:6600::17d3:8999). Similarly, Flipkart domains like static-assets-web.flipkart.com resolved to Akamai edge servers. This shows how large-scale websites use CDNs (Content Delivery Networks) and CNAME chains for performance and reliability.




### 2. TCP
- TCP Packet Analysis <br>


| # | Source IP    | Destination IP | Protocol    | Info Summary                                                 | Notes                                         |
| - | ------------ | -------------- | ----------- | ------------------------------------------------------------ | --------------------------------------------- |
| 1 | 192.168.1.6  | 18.178.156.214 | TCP         | `ACK`, `Seq=1 Ack=1`                                         | Acknowledging a previous segment              |
| 2 | 192.168.1.6  | Various (443)  | TCP         | `SYN`, `ACK`, `Keep-Alive`                                   | Standard TCP connection setup and maintenance |
| 3 | 192.168.1.6  | 34.250.67.152  | TCP/TLSv1.3 | `Client Hello`, `Server Hello`, TLS handshake                | Encrypted HTTPS communication begins          |
| 4 | 192.168.1.6  | 103.243.32.90  | TCP/TLSv1.3 | `Client Hello (flipkart.com)`                                | Connection to Flipkart server                 |
| 5 | 192.168.1.6  | 23.211.137.152 | TCP/TLSv1.3 | `Client Hello (static-assets-web.flipkart.com)`              | Likely content delivery or assets request     |
| 6 | External IPs | 192.168.1.6    | TCP         | `SYN-ACK`, `ACK`, `Application Data`                         | Standard TCP reply and TLS data packets       |
| 7 | 192.168.1.6  | External       | TCP         | `TCP ACKed unseen segment` / `Previous segment not captured` | Indicates packet loss or late capture         |



### Observation <br>
From the captured traffic, it is evident that the system at 192.168.1.6 is actively engaging in HTTPS communications with multiple external servers. The traffic includes several standard TCP handshakes (SYN, SYN-ACK, ACK), followed by TLS 1.3 negotiation packets like Client Hello and Server Hello. Secure connections were established with domains such as flipkart.com, as seen in the SNI (Server Name Indication) fields. Periodic Keep-Alive packets suggest long-lived HTTPS sessions. There are also signs of minor packet loss or analysis window mismatch, indicated by "ACKed unseen segment" and "Previous segment not captured" warnings. Overall, this analysis confirms the expected behavior of a client initiating and maintaining secure web sessions over TCP port 443.
  
### 3. TLS
- TLS Analysis <br>

| S. No. | Protocol | Common Info                    | Observations / Examples                                                                     |
| ------ | -------- | ------------------------------ | ------------------------------------------------------------------------------------------- |
| 1      | QUIC     | Initial, CRYPTO, PING, PADDING | QUIC is used extensively, showing multiple `CRYPTO`, `PING`, and `PADDING` frames.          |
| 2      | TLSv1.3  | Client Hello / Server Hello    | TLS handshake observed with SNI like `flipkart.com`, `flixcart.com`, `nr-data.net`.         |
| 3      | TLSv1.3  | Application Data               | Multiple `Application Data` packets indicate active encrypted communication.                |
| 4      | TLSv1.3  | Continuation Data              | Many continuation fragments seen – indicates large payloads segmented over several packets. |
| 5      | TLSv1.3  | TCP Retransmissions / ACKs     | TCP-level retransmissions detected, suggesting possible network congestion or latency.      |
| 6      | TCP      | Previous segment not captured  | Indicates packet loss or capture start after session began.                                 |


### Observation <br>
The captured network traffic indicates heavy usage of encrypted protocols such as **TLSv1.3** and **QUIC**, suggesting secure communication with several domains including Flipkart services `(flipkart.com, static-assets-web.flipkart.com)` and telemetry `(nr-data.net)`. The QUIC protocol packets prominently include multiple `CRYPTO, PING, and PADDING` frames, consistent with secure UDP-based transport optimized for latency. Meanwhile, TLSv1.3 sessions include typical handshake patterns `(Client Hello, Server Hello)` followed by large volumes of `Application Data` and `Continuation Data`, indicating active data exchanges over HTTPS. There are also TCP-related events like "Previous segment not captured" and "TCP Retransmissions," hinting at either network instability or dropped packets during capture. Overall, the data suggests a mix of secure browsing, and some degree of packet loss or delayed transmission.

---

## Attachments Inlude: <br>
dns.pcapng, tcp.pcapng, tls.pcapng in network analysis folder
### Note: This `.pcapng` file has been reviewed and contains only filtered, non-sensitive DNS/TCP/TLS traffic captured from public websites (like Bing and Flipkart). No personal data or authentication credentials are present.
