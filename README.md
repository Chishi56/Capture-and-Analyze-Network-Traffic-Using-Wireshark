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

  
### Observation<br> 
During DNS analysis, several queries were observed for domains like www.bing.com and www.flipkart.com. These queries used both A (IPv4) and AAAA (IPv6) records. The domain www.bing.com followed a CNAME redirection chain from trafficmanager.net to edgekey.net, eventually resolving through Akamai CDN (akamaiedge.net). This resolved to both an IPv4 address (23.212.164.211) and an IPv6 address (2600:140f:6600::17d3:8999). Similarly, Flipkart domains like static-assets-web.flipkart.com resolved to Akamai edge servers. This shows how large-scale websites use CDNs (Content Delivery Networks) and CNAME chains for performance and reliability.




### 1. TCP
### 1. TLS
