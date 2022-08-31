---
type: protocol
keywords:
- protocol
- ethernet
- network
---
# Transmission Control Protocol (TCP)

Most web servers TCP slow start algorithm starts by sending 10 TCP packets.

The maximum size of a TCP packet is 1500 bytes.

Each TCP packet uses 40 bytes in its header — 16 bytes for IP and an additional 24 bytes for TCP

That leaves 1460 bytes per TCP packet. `10 x 1460 = 14600 bytes` or roughly `14kB`!

https://www.tunetheweb.com/blog/critical-resources-and-the-first-14kb/